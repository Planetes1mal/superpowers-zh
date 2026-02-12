# 测试反模式 (Testing Anti-Patterns)

**加载此参考当：** 编写或更改测试、添加 mock，或忍不住向生产代码添加仅测试方法时。

## 概述

测试必须验证真实行为，不是 mock 行为。Mock 是隔离的手段，不是被测试的东西。

**核心原则：** 测试代码做什么，不是 mock 做什么。

**遵循严格的 TDD 可防止这些反模式。**

## 铁律

```
1. 绝不测试 mock 行为
2. 绝不向生产类添加仅测试方法
3. 绝不 mock 而不理解依赖
```

## 反模式 1：测试 Mock 行为

**违规：**
```typescript
// ❌ BAD: 测试 mock 存在
test('renders sidebar', () => {
  render(<Page />);
  expect(screen.getByTestId('sidebar-mock')).toBeInTheDocument();
});
```

**为什么这是错的：**
- 你在验证 mock 工作，不是组件工作
- 当 mock 存在时测试通过，当它不存在时失败
- 对真实行为一无所知

**您的人类伙伴的纠正：** "Are we testing the behavior of a mock?"

**修复：**
```typescript
// ✅ GOOD: 测试真实组件或不 mock 它
test('renders sidebar', () => {
  render(<Page />);  // 不要 mock sidebar
  expect(screen.getByRole('navigation')).toBeInTheDocument();
});

// OR if sidebar must be mocked for isolation:
// 不要断言 mock - 测试有 sidebar 的 Page 行为
```

### Gate Function

```
BEFORE asserting on any mock element:
  Ask: "Am I testing real component behavior or just mock existence?"

  IF testing mock existence:
    STOP - Delete the assertion or unmock the component

  Test real behavior instead
```

## 反模式 2：生产中的仅测试方法

**违规：**
```typescript
// ❌ BAD: destroy() 仅在测试中使用
class Session {
  async destroy() {  // 看起来像生产 API！
    await this._workspaceManager?.destroyWorkspace(this.id);
    // ... cleanup
  }
}

// 在测试中
afterEach(() => session.destroy());
```

**为什么这是错的：**
- 生产类被测试专用代码污染
- 如果在生产中意外调用很危险
- 违反 YAGNI 和关注点分离
- 混淆对象生命周期与实体生命周期

**修复：**
```typescript
// ✅ GOOD: 测试工具处理测试清理
// Session 没有 destroy() - 它在生产中是无状态的

// In test-utils/
export async function cleanupSession(session: Session) {
  const workspace = session.getWorkspaceInfo();
  if (workspace) {
    await workspaceManager.destroyWorkspace(workspace.id);
  }
}

// 在测试中
afterEach(() => cleanupSession(session));
```

### Gate Function

```
BEFORE adding any method to production class:
  Ask: "Is this only used by tests?"

  IF yes:
    STOP - Don't add it
    Put it in test utilities instead

  Ask: "Does this class own this resource's lifecycle?"

  IF no:
    STOP - Wrong class for this method
```

## 反模式 3：不理解就 Mock

**违规：**
```typescript
// ❌ BAD: Mock 破坏测试逻辑
test('detects duplicate server', () => {
  // Mock prevents config write that test depends on!
  vi.mock('ToolCatalog', () => ({
    discoverAndCacheTools: vi.fn().mockResolvedValue(undefined)
  }));

  await addServer(config);
  await addServer(config);  // Should throw - but won't!
});
```

**为什么这是错的：**
- Mocked 方法有测试依赖的副作用（写入 config）
- 过度 mock "为了安全"破坏了实际行为
- 测试因错误原因通过或神秘失败

**修复：**
```typescript
// ✅ GOOD: 在正确级别 mock
test('detects duplicate server', () => {
  // Mock the slow part, preserve behavior test needs
  vi.mock('MCPServerManager'); // Just mock slow server startup

  await addServer(config);  // Config written
  await addServer(config);  // Duplicate detected ✓
});
```

### Gate Function

```
BEFORE mocking any method:
  STOP - Don't mock yet

  1. Ask: "What side effects does the real method have?"
  2. Ask: "Does this test depend on any of those side effects?"
  3. Ask: "Do I fully understand what this test needs?"

  IF depends on side effects:
    Mock at lower level (the actual slow/external operation)
    OR use test doubles that preserve necessary behavior
    NOT the high-level method the test depends on

  IF unsure what test depends on:
    Run test with real implementation FIRST
    Observe what actually needs to happen
    THEN add minimal mocking at the right level

  Red flags:
    - "I'll mock this to be safe"
    - "This might be slow, better mock it"
    - Mocking without understanding the dependency chain
```

## 反模式 4：不完整的 Mock

**违规：**
```typescript
// ❌ BAD: 部分 mock - 只 mock 你认为需要的字段
const mockResponse = {
  status: 'success',
  data: { userId: '123', name: 'Alice' }
  // Missing: metadata that downstream code uses
};

// Later: breaks when code accesses response.metadata.requestId
```

**为什么这是错的：**
- **部分 mock 隐藏结构性假设** - 你只 mock 你知道的字段
- **下游代码可能依赖你没包含的字段** - 静默失败
- **测试通过但集成失败** - Mock 不完整，真实 API 完整
- **虚假置信** - 测试对真实行为一无所知

**铁律：** Mock 完整的**数据结构**如它在现实中存在，不只是你立即测试使用的字段。

**修复：**
```typescript
// ✅ GOOD: 镜像真实 API 完整性
const mockResponse = {
  status: 'success',
  data: { userId: '123', name: 'Alice' },
  metadata: { requestId: 'req-789', timestamp: 1234567890 }
  // 真实 API 返回的所有字段
};
```

### Gate Function

```
BEFORE creating mock responses:
  Check: "What fields does the real API response contain?"

  Actions:
    1. Examine actual API response from docs/examples
    2. Include ALL fields system might consume downstream
    3. Verify mock matches real response schema completely

  Critical:
    If you're creating a mock, you must understand the ENTIRE structure
    Partial mocks fail silently when code depends on omitted fields

  If uncertain: Include all documented fields
```

## 反模式 5：集成测试作为事后的想法

**违规：**
```
✅ 实现完成
❌ 没有编写测试
"准备测试"
```

**为什么这是错的：**
- 测试是实施的一部分，不是可选的后续
- TDD 本会捕获这个
- 没有测试不能声称完成

**修复：**
```
TDD 循环：
1. 编写失败测试
2. 实现通过
3. 重构
4. 然后声称完成
```

## 当 Mock 变得太复杂时

**警告信号：**
- Mock 设置比测试逻辑长
- Mock 一切让测试通过
- Mock 缺少真实组件有的方法
- Mock 变更时测试破坏

**您的人类伙伴的问题：** "Do we need to be using a mock here?"

**考虑：** 使用真实组件的集成测试通常比复杂 mock 更简单

## TDD 防止这些反模式

**为什么 TDD 有帮助：**
1. **先写测试** → 迫使你思考你实际在测试什么
2. **看着它失败** → 确认测试测试真实行为，不是 mock
3. **最小化实现** → 没有仅测试方法潜入
4. **真实依赖** → 你在 mock 前看到测试实际需要什么

**如果你在测试 mock 行为，你违反了 TDD** - 你在没有看着测试针对真实代码失败的情况下添加了 mock。

## 快速参考

| 反模式 | 修复 |
|--------------|-----|
| 断言 mock 元素 | 测试真实组件或不 mock 它 |
| 生产中的仅测试方法 | 移动到测试工具 |
| 不理解就 mock | 先理解依赖，最小化 mock |
| 不完整的 mock | 完全镜像真实 API |
| 测试作为事后的想法 | TDD - 测试优先 |
| 过度复杂的 mock | 考虑集成测试 |

## 警告标志

- 断言检查 `*-mock` 测试 ID
- 仅在测试文件中调用的方法
- Mock 设置是测试的 >50%
- 移除 mock 时测试失败
- 无法解释为什么需要 mock
- "为了安全"mock

## 底线

**Mock 是隔离的工具，不是测试的东西。**

如果 TDD 揭示你在测试 mock 行为，你就错了。

修复：测试真实行为或质疑为什么要 mock。
