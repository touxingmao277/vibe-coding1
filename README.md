# 贪吃蛇游戏 - Django 全栈开发

## 项目简介

这是一个基于 Django + 原生 JavaScript (Canvas) 实现的贪吃蛇游戏，采用全栈架构设计。

## 技术栈

### 后端 - Django
- **框架**: Django 6.x
- **功能**: 提供游戏首页、静态文件服务
- **路由**: URL 分发到游戏视图
- **模板引擎**: Django 自带模板系统

### 前端 - JavaScript + Canvas
- **渲染**: HTML5 Canvas 2D 绘图
- **游戏逻辑**: 原生 JavaScript 实现
- **样式**: CSS3（渐变、阴影、动画效果）

## 项目结构

```
snake_game/
├── manage.py                 # Django 命令行工具
├── README.md                 # 项目说明文档
├── snake_game/               # Django 项目配置目录
│   ├── __init__.py
│   ├── settings.py           # 项目配置（数据库、应用注册等）
│   ├── urls.py               # 项目路由配置
│   └── wsgi.py               # WSGI 服务器入口
└── game/                     # 游戏应用
    ├── __init__.py
    ├── views.py              # 视图函数（渲染游戏页面）
    ├── urls.py               # 应用路由配置
    └── templates/
        └── game/
            └── index.html    # 游戏主页面（含JS/CSS）
```

## 核心功能实现

### 1. 蛇移动控制

**实现逻辑**:
- 使用方向键（↑↓←→）控制移动方向
- 禁止 180 度原地掉头（通过比较当前方向与新方向实现）
- 蛇身通过数组维护，每次移动添加新头、移除尾（除非吃到食物）

```javascript
// 方向控制逻辑
switch (e.key) {
    case 'ArrowUp':
        if (direction.y !== 1) nextDirection = { x: 0, y: -1 };
        break;
    case 'ArrowDown':
        if (direction.y !== -1) nextDirection = { x: 0, y: 1 };
        break;
    // ... 其他方向
}
```

### 2. 食物生成与碰撞检测

**食物生成**:
- 随机生成坐标（0-19，共20x20网格）
- 检测是否与蛇身重叠，重叠则重新生成
- 最大尝试次数 2000 次，超过则游戏胜利（填满画布）

**碰撞检测**:
- **边界碰撞**: 检测蛇头坐标是否超出 0-19 范围
- **自身碰撞**: 检测蛇头是否与蛇身任意段重叠

```javascript
// 边界碰撞检测
if (head.x < 0 || head.x >= GRID_COUNT || 
    head.y < 0 || head.y >= GRID_COUNT) {
    gameOver();
}

// 自身碰撞检测
if (snake.some(segment => segment.x === head.x && segment.y === head.y)) {
    gameOver();
}
```

### 3. 分数系统

- 吃到食物分数 +1
- 最高分使用 localStorage 本地存储，持久化保存
- 显示当前分数、最高分、蛇长度

```javascript
// 吃到食物增加分数
score++;
if (score > highScore) {
    highScore = score;
    localStorage.setItem('snakeHighScore', highScore.toString());
}
```

### 4. 游戏状态管理

- `isGameRunning`: 游戏是否运行中
- `isPaused`: 是否暂停
- `gameLoop`: 游戏循环定时器（setInterval，150ms 间隔）

### 5. 键盘控制

| 按键 | 功能 |
|------|------|
| ↑↓←→ | 控制蛇移动方向 |
| Enter | 开始/重新开始游戏 |
| Space | 暂停/继续游戏 |

## 界面设计

### 视觉风格

- **背景**: 深绿色渐变 `#1e3c2c → #0f2a1f`
- **蛇身**: 绿色渐变发光效果
- **食物**: 红色圆形发光
- **按钮**: 玻璃态半透明设计，悬停动画效果

### 响应式设计

- 支持移动端显示
- 自适应画布尺寸

## 运行方式

### 1. 安装依赖

```bash
# 确保已安装 Django
pip install django
```

### 2. 启动开发服务器

```bash
cd snake_game
python manage.py runserver
```

### 3. 访问游戏

打开浏览器访问: `http://127.0.0.1:8000`

## 游戏玩法

1. 点击「开始游戏」按钮或按 Enter 键开始
2. 使用方向键控制蛇的移动方向
3. 吃到红色食物蛇会变长，分数增加
4. 避免撞墙或撞到自己的身体
5. 按空格键可以暂停/继续游戏
6. 游戏结束后按 Enter 键或点击「重新开始」按钮重新开始

## 开发说明

### 代码组织

- **后端**: 仅负责页面渲染，游戏逻辑完全在前端实现
- **前端**: 所有游戏逻辑（蛇移动、碰撞检测、计分等）均使用原生 JavaScript 实现
- **模板**: CSS 和 JavaScript 内嵌在 HTML 中，便于查看和维护

### 扩展建议

1. **数据库集成**: 添加用户注册登录，保存历史最高分
2. **多人模式**: 使用 WebSocket 实现双人对战
3. **难度系统**: 随分数增加提高游戏速度
4. **皮肤系统**: 支持多种蛇和食物外观
5. **音效系统**: 添加游戏音效和背景音乐

## 许可证

MIT License