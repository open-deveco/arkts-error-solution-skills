# Module and Override Errors

## Error: Cannot Find Module with Override Modifier Conflict

### Error Message
    Cannot find module 'xxx' or its corresponding type declarations (arkts-cannot-find-module)
    This member cannot have an 'override' modifier


### Cause
When both errors appear simultaneously, the root cause is typically the module import failure. Because the parent class module cannot be found, the compiler cannot verify the inheritance relationship, which causes the override modifier to fail validation as a secondary effect.

### Solution
Must fix the Cannot find module error first, then verify if the override error resolves automatically.

### Key Points
 - When both errors appear together, always fix Cannot find module first
 - The override error is usually a secondary symptom caused by the missing module
 - Check that relative paths and absolute paths point to the same file
 - After fixing the import path, the override error may disappear automatically
 - If the override error persists after fixing the import, verify the parent class has the corresponding method


### ❌ Wrong Usage
```typescript
// ❌ Wrong: Incorrect module path causes both errors
import { BaseComponent } from '../wrong/path/BaseComponent';  // Cannot find module

@Component
struct ChildComponent extends BaseComponent {
  override onInit(): void {  // This member cannot have an 'override' modifier
    super.onInit();
  }
}

// ❌ Wrong: Mixing relative and absolute paths incorrectly
import { BaseClass } from 'src/common/BaseClass';  // Path mismatch

class ChildClass extends BaseClass {
  override getData(): string {  // Override error due to missing module
    return 'data';
  }
}
```

### ✅ Correct Usage
```typescript
// ✅ Correct: Fix the module path first
import { BaseComponent } from '../../common/BaseComponent';  // Correct path

@Component
struct ChildComponent extends BaseComponent {
  override onInit(): void {  // Now resolves correctly
    super.onInit();
  }
}

// ✅ Correct: Use consistent relative paths
import { BaseClass } from '../common/BaseClass';  // Correct relative path

class ChildClass extends BaseClass {
  override getData(): string {  // Override works after module is found
    return 'data';
  }
}
```



### Step-by-Step Fix Guide
Step 1: Fix Cannot find module Error
 ```typescript
    / Check your project structure:
    // project/
    // ├── src/
    // │   ├── common/
    // │   │   └── BaseComponent.ets   ← target file
    // │   └── pages/
    // │       └── MyPage.ets          ← current file

    // ❌ Wrong path
    import { BaseComponent } from './BaseComponent';

    // ✅ Correct path (relative to current file)
    import { BaseComponent } from '../common/BaseComponent';
 ```
Step 2: Verify if override error resolves automatically
```typescript
// BaseComponent.ets (parent class)
export class BaseComponent {
  onInit(): void {
    console.info('Base onInit');
  }

  getData(): string {
    return 'base data';
  }
}
// ✅ After fixing import path, override works correctly
import { BaseComponent } from '../common/BaseComponent';

class ChildComponent extends BaseComponent {
  override onInit(): void {      // ✅ Parent method exists, override is valid
    super.onInit();
    console.info('Child onInit');
  }

  override getData(): string {   // ✅ Parent method exists, override is valid
    return 'child data';
  }
}

```

### Path Consistency Rules
#### Relative Path vs Absolute Path
```typescript
// 项目根目录: /home/user/project

// ❌ Wrong: 路径引用方式不一致
import { Base } from 'project/src/common/Base';    // 类绝对路径写法
import { Utils } from '../utils/Utils';             // 相对路径写法
// 两者可能解析到不同位置

// ✅ Correct: 统一使用相对路径
import { Base } from '../common/Base';
import { Utils } from '../utils/Utils';

// ❌ Wrong: 在需要相对路径的地方使用了绝对路径
import { Config } from '/src/config/Config';

// ✅ Correct: 相对于当前文件位置的正确路径
import { Config } from '../../config/Config';
```

#### Common Scenarios
##### Scenario 1: Renamed or Moved File
```typescript
// ❌ Old path no longer valid after file move
import { BaseService } from '../services/BaseService';

// ✅ Updated path after file was moved
import { BaseService } from '../../core/services/BaseService';
```
##### Scenario 2: Wrong File Extension
```typescript
// ❌ Wrong: Missing or wrong extension in some environments
import { BaseModel } from '../models/BaseModel.ts';

// ✅ Correct: Use .ets for ArkTS files
import { BaseModel } from '../models/BaseModel';
```

##### Scenario 3: Export Name Mismatch
```typescript
// BaseHelper.ets
export class BaseHelper {  // ← exported name
  help(): void {}
}

// ❌ Wrong: Importing with wrong name
import { Helper } from '../common/BaseHelper';

// ✅ Correct: Match the exported class name
import { BaseHelper } from '../common/BaseHelper';

class ChildHelper extends BaseHelper {
  override help(): void {
    super.help();
  }
}
```

### Best Practices
Fix module errors first: Always resolve Cannot find module before investigating override errors
Use relative paths consistently: Keep import path styles consistent throughout the project
Verify parent class: After fixing imports, confirm the parent class actually declares the method being overridden
Keep override keyword: If the parent method exists, always retain the override keyword in the child class
Check file structure: Use IDE file tree to verify actual file locations before writing import paths

