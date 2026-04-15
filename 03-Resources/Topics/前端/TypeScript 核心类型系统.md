---
tags:
- frontend
- typescript
- language
type: resource
status: active
created: 2026-04-14
updated: '2026-04-14'
source_type: migrated_note
source_path: 面试/基础/基础-TypeScript 核心类型系统.md
---
# TypeScript 核心类型系统

> 本文梳理 TypeScript 类型系统的核心概念，涵盖 any/unknown、keyof、工具类型、条件类型、infer 等面试常考知识点。

---

## 一、any vs unknown

### 1.1 any：放弃类型安全

```typescript
let anything: any = 42;

// any 允许任何操作，不进行类型检查
anything.foo.bar;      // 不报错，运行时可能出错
anything();            // 不报错
anything[0];           // 不报错
anything.toFixed();    // 不报错
```

**特点**：
- 完全绕过类型检查
- 可以访问任意属性、调用任意方法
- 编译时不报错，运行时可能崩溃
- **慎用**，除非是：
  - 迁移旧代码
  - 处理动态数据
  - 类型确实无法确定

### 1.2 unknown：类型安全的 any

```typescript
let something: unknown = 42;

// unknown 不允许任意操作
something.foo.bar;     // ❌ 报错：Object is of type 'unknown'
something();           // ❌ 报错
something[0];          // ❌ 报错

// ✅ 正确用法：先进行类型收窄
if (typeof something === 'string') {
  something.toUpperCase();  // ✅ 这里 something 被收窄为 string
}

if (typeof something === 'number') {
  something.toFixed(2);     // ✅ 这里 something 被 收窄为 number
}

// 使用类型断言
(something as string).toUpperCase();  // ✅ 但断言是运行时风险
```

### 1.3 核心区别

| 特性 | any | unknown |
|------|-----|---------|
| 类型检查 | ❌ 放弃 | ✅ 保留 |
| 任意属性访问 | ✅ 允许 | ❌ 禁止 |
| 任意方法调用 | ✅ 允许 | ❌ 禁止 |
| 必须先类型收窄 | ❌ 不需要 | ✅ 必须 |
| 适合场景 | 紧急情况 | 动态值处理 |

**记忆口诀**：
- `any` = "我不检查，运行时自求多福"
- `unknown` = "我知道这不是 any，但交给使用者判断"

---

## 二、keyof 操作符

### 2.1 基本用法

`keyof` 获取对象所有 key 的联合类型：

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

type UserKeys = keyof User;
// 等价于：type UserKeys = 'id' | 'name' | 'email'
```

### 2.2 实际应用：安全的属性访问

```typescript
function getValue<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user: User = { id: 1, name: 'Alice', email: 'alice@example.com' };

// ✅ 合法
getValue(user, 'name');     // 返回 string
getValue(user, 'id');       // 返回 number

// ❌ 编译时报错，因为 'age' 不是 User 的 key
getValue(user, 'age');      // Argument of type '"age"' is not assignable to parameter of type 'keyof User'
```

### 2.3 keyof + 泛型实现类型安全的配置

```typescript
function setConfig<T extends object>(obj: T, key: keyof T, value: T[keyof T]) {
  obj[key] = value;
}

const config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
  retries: 3
};

setConfig(config, 'apiUrl', 'https://new.api.com'); // ✅
setConfig(config, 'timeout', 10000);                // ✅
setConfig(config, 'unknown', 123);                  // ❌ 编译报错
```

---

## 三、工具类型（Utility Types）

### 3.1 工具类型本质

TypeScript 官方预置的泛型类型工具，本质上是**映射类型 + 条件类型**的组合。

### 3.2 核心：Record

**作用**：快速声明一个对象类型，指定所有「键的类型」和「值的类型」。

```typescript
type Record<K extends string | number | symbol, T> = {
  [P in K]: T;
};
```

**使用示例**：

```typescript
// 场景1：定义一个字符串键、数字值的映射
type PageInfo = Record<string, number>;

const pageSizes: PageInfo = {
  mobile: 10,
  tablet: 20,
  desktop: 50,
};

// 场景2：定义状态枚举映射
type StatusType = 'pending' | 'success' | 'error';

type StatusLabels = Record<StatusType, string>;

const labels: StatusLabels = {
  pending: '处理中',
  success: '成功',
  error: '失败',
};

// 场景3：定义用户权限映射
type Permission = 'read' | 'write' | 'delete';
type RolePermissions = Record<Permission, boolean>;

const adminPermissions: RolePermissions = {
  read: true,
  write: true,
  delete: true,
};
```

### 3.3 其他常用工具类型

| 工具类型 | 作用 | 底层实现 |
|---------|------|---------|
| **Partial** | 所有属性变为可选 | `{ [K in keyof T]?: T[K] }` |
| **Required** | 所有属性变为必需 | `{ [K in keyof T]-?: T[K] }` |
| **Readonly** | 所有属性变为只读 | `{ [K in keyof T]: Readonly<T[K]> }` |
| **Pick<T, K>** | 只保留指定的属性 | `{ [K in K]: T[K] }` |
| **Omit<T, K>** | 排除指定的属性 | `Pick<T, Exclude<keyof T, K>>` |

**示例**：

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// Partial：所有属性可选
type PartialUser = Partial<User>;
// 等价于：{ id?: number; name?: string; email?: string; age?: number; }

// Pick：只保留 id 和 name
type UserPreview = Pick<User, 'id' | 'name'>;
// 等价于：{ id: number; name: string; }

// Omit：排除 age
type UserWithoutAge = Omit<User, 'age'>;
// 等价于：{ id: number; name: string; email: string; }

// Required：所有属性必需
type RequiredUser = Required<PartialUser>;
// 等价于：{ id: number; name: string; email: string; age: number; }

// Readonly：所有属性只读
type ReadonlyUser = Readonly<User>;
// 等价于：{ readonly id: number; readonly name: string; ... }
```

### 3.4 底层实现：映射类型

**映射类型语法**：

```typescript
// 基本形式
type MyPartial<T> = {
  [K in keyof T]?: T[K];
};

// +? 表示添加可选，-? 表示移除可选
type MyRequired<T> = {
  [K in keyof T]-?: T[K];
};

// +readonly 表示添加只读，-readonly 表示移除只读
type MyReadonly<T> = {
  +readonly [K in keyof T]: T[K];
};

type Mutable<T> = {
  -readonly [K in keyof T]: T[K];
};
```

**组合使用**：

```typescript
// 只读 + 必需
type ReadonlyRequired<T> = {
  +readonly [K in keyof T]-?: T[K];
};

// 可选 + 可变
type OptionalMutable<T> = {
  -readonly [K in keyof T]?: T[K];
};
```

---

## 四、条件类型（Conditional Types）

### 4.1 基本语法

```typescript
T extends U ? X : Y
```

类似三元运算符，根据类型关系选择不同类型。

**示例**：

```typescript
// 根据类型选择返回值
type IsString<T> = T extends string ? true : false;

type A = IsString<string>;   // true
type B = IsString<number>;   // false
type C = IsString<'hello'>;  // true（字面量也是 string）

// 实用场景：提取数组元素类型
type ElementType<T> = T extends (infer U)[] ? U : never;

type Arr1 = ElementType<string[]>;      // string
type Arr2 = ElementType<number[]>;      // number
type Arr3 = ElementType<string>;        // never
```

### 4.2 条件类型的分发特性

**对联合类型会自动分发**：

```typescript
// T 是联合类型时，会分发到每个成员
type ToArray<T> = T extends any ? T[] : never;

type Result = ToArray<string | number>;
// 等价于：ToArray<string> | ToArray<number>
// 结果：string[] | number[]

// 实用示例：过滤联合类型
type OnlyString<T> = T extends string ? T : never;

type Filtered = OnlyString<string | number | boolean | 'hello'>;
// 等价于：string | 'hello'
```

**禁止分发**：

```typescript
// 使用 [] 包裹类型参数，禁止分发
type NoDistribute<T> = [T] extends [any] ? T[] : never;

type Result = NoDistribute<string | number>;
// 结果：(string | number)[]
```

### 4.3 条件类型收窄

```typescript
// 多层条件类型
type TypeName<T> =
  T extends string ? 'string' :
  T extends number ? 'number' :
  T extends boolean ? 'boolean' :
  T extends undefined ? 'undefined' :
  T extends Function ? 'function' :
  'object';

type T1 = TypeName<string>;    // 'string'
type T2 = TypeName<number[]>;  // 'object'
type T3 = TypeName<(() => void)>; // 'function'
```

---

## 五、infer 关键字

### 5.1 作用

`infer` 用于在条件类型中**推断类型变量**，提取类型的内部结构。

### 5.2 提取函数返回值类型

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function getUser() {
  return { id: 1, name: 'Alice' };
}

type User = ReturnType<typeof getUser>;
// 等价于：{ id: number; name: string; }
```

### 5.3 提取函数参数类型

#### 5.3.1 提取所有参数元组

```typescript
// 获取函数所有参数的类型元组
type GetParams<T> = T extends (...args: infer P) => any ? P : never;

type Fn = (a: string, b: number) => void;
type FnParams = GetParams<Fn>;
// 等价于：[string, number]

// 实际使用
function createUser(name: string, age: number, email: string) {
  return { name, age, email };
}

type CreateUserParams = GetParams<typeof createUser>;
// 等价于：[string, number, string]

// 可以配合索引访问获取特定位置参数
type FirstParam = CreateUserParams[0]; // string
type SecondParam = CreateUserParams[1]; // number
```

#### 5.3.2 提取第一个参数

```typescript
type FirstParameter<T> = T extends (first: infer F, ...rest: any[]) => any ? F : never;

function greet(name: string, age: number) {
  console.log(`Hello ${name}, you are ${age}`);
}

type Name = FirstParameter<typeof greet>;
// 等价于：string
```

#### 5.3.3 提取最后一个参数

```typescript
type LastParameter<T> = T extends (...args: any[], last: infer L) => any ? L : never;

function log(message: string, level: string, timestamp: Date) {
  console.log(`[${level}] ${message} at ${timestamp}`);
}

type Timestamp = LastParameter<typeof log>;
// 等价于：Date
```

#### 5.3.4 实用案例：函数参数透传

```typescript
// 高阶函数保持原函数参数类型
function withLogging<T extends (...args: any[]) => any>(fn: T): T {
  return ((...args: GetParams<T>) => {
    console.log('Calling with:', args);
    const result = fn(...args);
    console.log('Result:', result);
    return result;
  }) as T;
}

function add(a: number, b: number): number {
  return a + b;
}

const loggedAdd = withLogging(add);
// loggedAdd 的类型自动推导为 (a: number, b: number) => number

loggedAdd(1, 2); // ✅ 类型安全
loggedAdd('1', 2); // ❌ 编译报错
```

### 5.4 提取 Promise 返回值

```typescript
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type P1 = UnwrapPromise<Promise<string>>;   // string
type P2 = UnwrapPromise<Promise<number[]>>; // number[]
type P3 = UnwrapPromise<string>;            // string
```

### 5.5 提取数组元素类型

```typescript
type ElementType<T> = T extends (infer U)[] ? U : never;

type E1 = ElementType<string[]>;   // string
type E2 = ElementType<number[]>;   // number
type E3 = ElementType<number>;     // never
```

### 5.6 实用案例：提取 this 参数

```typescript
type ThisParameterType<T> = T extends (this: infer U, ...args: any[]) => any ? U : unknown;

function greet(this: { name: string }) {
  console.log(`Hello, ${this.name}`);
}

type GreetThis = ThisParameterType<typeof greet>;
// 等价于：{ name: string }
```

---

## 六、综合实战：类型工具链

### 6.1 DeepPartial：深层 Partial

```typescript
type DeepPartial<T> = {
  [K in keyof T]?: T[K] extends object
    ? DeepPartial<T[K]>
    : T[K];
};

interface Config {
  server: {
    host: string;
    port: number;
  };
  database: {
    url: string;
    credentials: {
      user: string;
      password: string;
    };
  };
}

const partialConfig: DeepPartial<Config> = {
  server: {
    // port 可选
    host: 'localhost',
  },
  database: {
    url: 'mongodb://localhost',
    credentials: {
      // password 可选
      user: 'admin',
    },
  },
};
```

### 6.2 DeepReadonly：深层只读

```typescript
type DeepReadonly<T> = {
  readonly [K in keyof T]: T[K] extends object
    ? DeepReadonly<T[K]>
    : T[K];
};

const readonlyConfig: DeepReadonly<Config> = {
  server: {
    host: 'localhost',
    port: 8080,
  },
  database: {
    url: 'mongodb://localhost',
    credentials: {
      user: 'admin',
      password: 'secret',
    },
  },
};

// 以下全部报错
readonlyConfig.server.port = 9090;
readonlyConfig.database.credentials.password = 'new-secret';
```

### 6.3 GetOptionalFields：提取可选字段

```typescript
type GetOptionalFields<T> = {
  [K in keyof T]-?: {} extends Pick<T, K> ? K : never;
}[keyof T];

interface User {
  id: number;
  name: string;
  age?: number;
  email?: string;
}

type OptionalFields = GetOptionalFields<User>;
// 等价于：'age' | 'email'
```

### 6.4 PromiseValue：提取 Promise 值（支持多层）

```typescript
type PromiseValue<T> = T extends Promise<infer U>
  ? PromiseValue<U>
  : T;

async function fetchUser() {
  return { id: 1, name: 'Alice' };
}

type User = PromiseValue<ReturnType<typeof fetchUser>>;
// 等价于：{ id: number; name: string; }

// 多层 Promise
type DeepPromise = Promise<Promise<Promise<string>>>;
type Unwrapped = PromiseValue<DeepPromise>;
// 等价于：string
```

---

## 七、面试常问总结

### 7.1 问答速查

| 问题 | 回答要点 |
|------|---------|
| **any vs unknown** | any 放弃类型检查，unknown 要求类型收窄后再使用 |
| **keyof 作用** | 获取对象所有 key 的联合类型，配合泛型实现类型安全的属性访问 |
| **Record 用法** | `Record<Keys, Values>` 定义键值对类型，Keys 是联合类型，Values 是统一值类型 |
| **工具类型本质** | 映射类型 + 条件类型，遍历属性并应用转换规则 |
| **条件类型分发** | `T extends U` 当 T 是联合类型时，自动分发到每个成员 |
| **infer 用法** | 在条件类型中推断类型变量，用于提取内部类型（返回值、参数元组、Promise 值、数组元素等） |

### 7.2 手写实现速记

```typescript
// Partial
type MyPartial<T> = { [K in keyof T]?: T[K] };

// Required
type MyRequired<T> = { [K in keyof T]-?: T[K] };

// Readonly
type MyReadonly<T> = { readonly [K in keyof T]: T[K] };

// Pick
type MyPick<T, K extends keyof T> = { [P in K]: T[P] };

// Omit
type MyOmit<T, K extends keyof T> = MyPick<T, Exclude<keyof T, K>>;

// Record
type MyRecord<K extends string | number | symbol, T> = { [P in K]: T };

// Exclude
type MyExclude<T, U> = T extends U ? never : T;

// Extract
type MyExtract<T, U> = T extends U ? T : never;

// ReturnType
type MyReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : never;

// Parameters（官方工具类型，获取函数所有参数的元组类型）
type MyParameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;
```

---

## 标签

#TypeScript #前端 #面试 #类型系统

