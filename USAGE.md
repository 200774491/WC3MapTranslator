# WC3MapTranslator 使用指南

## 概述
WC3MapTranslator 是一个 TypeScript/JavaScript 库，用于在魔兽争霸3地图文件（.w3x）的 war3map 格式和 JSON 格式之间进行转换。这使得地图数据变得可读且易于修改，非常适合在 Git 仓库中存储 WC3 地图并检查差异。

## 安装

### 1. 安装依赖项
```bash
npm install wc3maptranslator
```

**要求：Node.js ≥ 14**

### 2. 项目开发设置

#### 克隆项目并安装依赖
```bash
git clone https://github.com/ChiefOfGxBxL/WC3MapTranslator.git
cd WC3MapTranslator
npm install
```

#### 构建项目
```bash
npm run build
```

#### 运行测试
```bash
npm run test
```

#### 代码检查
```bash
npm run lint
```

## 基本用法

### 导入库
```typescript
import {
  CamerasTranslator,
  DoodadsTranslator,
  ImportsTranslator,
  InfoTranslator,
  ObjectsTranslator,
  RegionsTranslator,
  SoundsTranslator,
  StringsTranslator,
  TerrainTranslator,
  UnitsTranslator
} from 'wc3maptranslator';
```

或者在 JavaScript 中：
```javascript
const {
  CamerasTranslator,
  DoodadsTranslator,
  // ... 其他翻译器
} = require('wc3maptranslator');
```

## 主要功能

### 1. 相机 (Cameras) - war3map.w3c

#### JSON 转 War3 格式
```typescript
import { CamerasTranslator } from 'wc3maptranslator';
import * as fs from 'fs';

// 定义相机数据
const cameras = [
  {
    "target": {
      "x": -319.01,
      "y": -90.18
    },
    "offsetZ": 0,
    "rotation": 90,
    "aoa": 304,
    "distance": 2657.34,
    "roll": 5,
    "fov": 70,
    "farClipping": 5000,
    "name": "MyCamera1"
  }
];

// 转换为 War3 格式
const result = CamerasTranslator.jsonToWar(cameras);

// 保存到文件
fs.writeFileSync('war3map.w3c', result.buffer);
```

#### War3 格式转 JSON
```typescript
import { CamerasTranslator } from 'wc3maptranslator';
import * as fs from 'fs';

// 读取 war3map.w3c 文件
const buffer = fs.readFileSync('war3map.w3c');

// 转换为 JSON
const cameras = CamerasTranslator.warToJson(buffer);

console.log(JSON.stringify(cameras, null, 2));
```

### 2. 单位 (Units) - war3mapUnits.doo

```typescript
import { UnitsTranslator } from 'wc3maptranslator';

// 定义单位数据
const units = [
  {
    "type": "hfoo", // 人族步兵
    "variation": 0,
    "position": {
      "x": 100.0,
      "y": 200.0,
      "z": 0.0
    },
    "rotation": 0.0,
    "scale": {
      "x": 1.0,
      "y": 1.0,
      "z": 1.0
    },
    "hp": 100,
    "mp": 0,
    "player": 0,
    "abilityList": [],
    "inventoryList": []
  }
];

// 转换并保存
const result = UnitsTranslator.jsonToWar(units);
fs.writeFileSync('war3mapUnits.doo', result.buffer);
```

### 3. 地形装饰 (Doodads) - war3map.doo

```typescript
import { DoodadsTranslator } from 'wc3maptranslator';

const doodads = [
  {
    "type": "ATtr", // 树木类型
    "variation": 0,
    "position": {
      "x": 150.0,
      "y": 250.0,
      "z": 0.0
    },
    "rotation": 0.0,
    "scale": {
      "x": 1.0,
      "y": 1.0,
      "z": 1.0
    },
    "state": 0,
    "life": 100
  }
];

const result = DoodadsTranslator.jsonToWar(doodads);
fs.writeFileSync('war3map.doo', result.buffer);
```

### 4. 地图信息 (Info) - war3map.w3i

```typescript
import { InfoTranslator } from 'wc3maptranslator';

const mapInfo = {
  "name": "我的地图",
  "author": "地图作者",
  "description": "这是一个测试地图",
  "recommendedPlayers": "1v1",
  "playableWidth": 480,
  "playableHeight": 480,
  "players": [
    {
      "id": 0,
      "name": "玩家 1",
      "type": 1, // 人类玩家
      "race": 1, // 人族
      "startingPosition": {
        "x": 100,
        "y": 100
      }
    }
  ]
};

const result = InfoTranslator.jsonToWar(mapInfo);
fs.writeFileSync('war3map.w3i', result.buffer);
```

### 5. 字符串 (Strings) - war3map.wts

```typescript
import { StringsTranslator } from 'wc3maptranslator';

const strings = [
  {
    "id": 1,
    "value": "欢迎来到我的地图！"
  },
  {
    "id": 2, 
    "value": "任务目标：消灭所有敌人"
  }
];

const result = StringsTranslator.jsonToWar(strings);
fs.writeFileSync('war3map.wts', result.buffer);
```

### 6. 自定义对象 (Objects)

#### 自定义单位
```typescript
import { ObjectsTranslator } from 'wc3maptranslator';

const customUnits = {
  "original": [],
  "custom": [
    {
      "baseId": "hfoo", // 基于人族步兵
      "newId": "h001", // 新的单位ID
      "modifications": [
        {
          "id": "uhp0", // 生命值属性
          "type": "int",
          "level": 0,
          "column": 0,
          "value": 500 // 设置生命值为500
        },
        {
          "id": "unam", // 单位名称
          "type": "string", 
          "level": 0,
          "column": 0,
          "value": "超级步兵"
        }
      ]
    }
  ]
};

const result = ObjectsTranslator.jsonToWar(customUnits);
fs.writeFileSync('war3map.w3u', result.buffer);
```

## 完整工作流程示例

### 从地图提取数据并修改
```typescript
import * as fs from 'fs';
import { UnitsTranslator, DoodadsTranslator } from 'wc3maptranslator';

// 1. 读取现有地图文件
const unitsBuffer = fs.readFileSync('原始地图/war3mapUnits.doo');
const doodadsBuffer = fs.readFileSync('原始地图/war3map.doo');

// 2. 转换为JSON格式
const units = UnitsTranslator.warToJson(unitsBuffer);
const doodads = DoodadsTranslator.warToJson(doodadsBuffer);

// 3. 修改数据
// 增加一个新单位
units.push({
  "type": "hkni", // 人族骑士
  "position": { "x": 300, "y": 300, "z": 0 },
  "rotation": 0,
  "scale": { "x": 1, "y": 1, "z": 1 },
  "hp": 100,
  "mp": 0,
  "player": 0
});

// 修改第一个单位的位置
if (units.length > 0) {
  units[0].position.x = 500;
  units[0].position.y = 500;
}

// 4. 转换回war3格式
const newUnitsResult = UnitsTranslator.jsonToWar(units);
const newDoodadsResult = DoodadsTranslator.jsonToWar(doodads);

// 5. 保存修改后的文件
fs.writeFileSync('修改后地图/war3mapUnits.doo', newUnitsResult.buffer);
fs.writeFileSync('修改后地图/war3map.doo', newDoodadsResult.buffer);

console.log('地图修改完成！');
```

## 支持的文件格式

### 世界文件 (World Files)
- ✅ **地形** (war3map.w3e) - JSON→War
- ✅ **单位** (war3mapUnits.doo) - 双向转换
- ✅ **装饰物** (war3map.doo) - 双向转换
- ✅ **区域** (war3map.w3r) - 双向转换
- ✅ **相机** (war3map.w3c) - 双向转换
- ✅ **声音** (war3map.w3s) - 双向转换

### 对象数据文件 (Object Data Files)
- ✅ **单位对象** (war3map.w3u) - 双向转换
- ✅ **物品对象** (war3map.w3t) - 双向转换
- ✅ **技能对象** (war3map.w3a) - 双向转换
- ✅ **可破坏物对象** (war3map.w3b) - 双向转换
- ✅ **装饰物对象** (war3map.w3d) - 双向转换
- ✅ **升级对象** (war3map.w3q) - 双向转换
- ✅ **魔法效果对象** (war3map.w3h) - 双向转换

### 地图文件 (Map Files)
- ✅ **地图信息** (war3map.w3i) - 双向转换
- ✅ **导入文件** (war3map.imp) - 双向转换
- ✅ **字符串** (war3map.wts) - 双向转换

## 常见问题和技巧

### 1. 文件编码问题
确保在读取/写入文件时使用正确的编码：
```typescript
// 正确的方式读取二进制文件
const buffer = fs.readFileSync('war3map.w3c'); // 不指定编码，返回Buffer

// 写入时也不指定编码
fs.writeFileSync('war3map.w3c', result.buffer);
```

### 2. 错误处理
```typescript
try {
  const result = CamerasTranslator.jsonToWar(cameras);
  fs.writeFileSync('war3map.w3c', result.buffer);
  console.log('转换成功！');
} catch (error) {
  console.error('转换失败：', error.message);
}
```

### 3. 批量处理文件
```typescript
import * as path from 'path';

// 批量转换目录中的所有.doo文件
const mapDir = './maps';
const files = fs.readdirSync(mapDir);

files.forEach(file => {
  if (path.extname(file) === '.doo') {
    const filePath = path.join(mapDir, file);
    const buffer = fs.readFileSync(filePath);
    
    if (file === 'war3map.doo') {
      const doodads = DoodadsTranslator.warToJson(buffer);
      console.log(`${file} 包含 ${doodads.length} 个装饰物`);
    } else if (file === 'war3mapUnits.doo') {
      const units = UnitsTranslator.warToJson(buffer);
      console.log(`${file} 包含 ${units.length} 个单位`);
    }
  }
});
```

## 开发和贡献

### 项目结构
```
WC3MapTranslator/
├── src/                    # 源代码
│   ├── translators/        # 各种翻译器
│   ├── HexBuffer.ts       # 十六进制缓冲区工具
│   ├── W3Buffer.ts        # War3 缓冲区工具
│   └── index.ts           # 主入口文件
├── test/                   # 测试文件
├── dist/                   # 编译后的文件
└── package.json
```

### 开发工作流程
1. 修改源代码
2. 运行测试：`npm run test`
3. 构建项目：`npm run build`
4. 检查代码风格：`npm run lint`

### VS Code 任务
项目配置了两个 VS Code 任务：
- **安装依赖**：运行 `npm install`
- **运行测试脚本**：运行 `npm run test`

可以通过 Ctrl+Shift+P → "Tasks: Run Task" 来使用这些任务。

## 更多资源

- [官方GitHub仓库](https://github.com/ChiefOfGxBxL/WC3MapTranslator)
- [WC3MapTranslator格式文档](https://github.com/ChiefOfGxBxL/WC3MapTranslator/wiki)
- [war3map格式规范](https://github.com/ChiefOfGxBxL/WC3MapSpecification)
- [NPM包页面](https://www.npmjs.com/package/wc3maptranslator)

## 许可证
MIT License - 详见 LICENSE.md 文件
