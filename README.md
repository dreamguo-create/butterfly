# butterfly
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>仿生蝴蝶 - 哈尔滨冰雪大世界</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Font Awesome -->
  <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
  <!-- Three.js -->
  <script src="https://cdn.jsdelivr.net/npm/three@0.132.2/build/three.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/three@0.132.2/examples/js/controls/OrbitControls.js"></script>
  <!-- GSAP 动画库 -->
  <script src="https://cdn.jsdelivr.net/npm/gsap@3.11.4/dist/gsap.min.js"></script>
  
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            primary: '#0066cc',
            secondary: '#00aaff',
            accent: '#ff3300',
            ice: '#e6f7ff',
            dark: '#001a33'
          },
          fontFamily: {
            sans: ['Arial', 'sans-serif'],
          },
          animation: {
            'float': 'float 3s ease-in-out infinite',
            'pulse-slow': 'pulse 4s cubic-bezier(0.4, 0, 0.6, 1) infinite',
          },
          keyframes: {
            float: {
              '0%, 100%': { transform: 'translateY(0)' },
              '50%': { transform: 'translateY(-10px)' },
            }
          }
        }
      }
    }
  </script>
  
  <style type="text/tailwindcss">
    @layer utilities {
      .text-shadow {
        text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5);
      }
      .text-shadow-lg {
        text-shadow: 0 4px 8px rgba(0, 0, 0, 0.8);
      }
      .bg-gradient-ice {
        background: linear-gradient(to bottom, #001a33, #003366);
      }
      .bg-glass {
        background: rgba(255, 255, 255, 0.1);
        backdrop-filter: blur(10px);
        -webkit-backdrop-filter: blur(10px);
      }
      .border-glass {
        border: 1px solid rgba(255, 255, 255, 0.2);
      }
    }
    
    body {
      overflow-x: hidden;
      background-color: #001a33;
      color: white;
    }
    
    #butterfly-container {
      position: relative;
      width: 100%;
      height: 100vh;
      overflow: hidden;
    }
    
    .hotspot {
      position: absolute;
      width: 20px;
      height: 20px;
      border-radius: 50%;
      background-color: rgba(255, 51, 0, 0.8);
      cursor: pointer;
      transform: translate(-50%, -50%);
      z-index: 10;
      box-shadow: 0 0 0 rgba(255, 51, 0, 0.4);
      animation: pulse 2s infinite;
    }
    
    @keyframes pulse {
      0% {
        box-shadow: 0 0 0 0 rgba(255, 51, 0, 0.4);
      }
      70% {
        box-shadow: 0 0 0 10px rgba(255, 51, 0, 0);
      }
      100% {
        box-shadow: 0 0 0 0 rgba(255, 51, 0, 0);
      }
    }
    
    .info-card {
      position: absolute;
      width: 300px;
      background: rgba(0, 26, 64, 0.9);
      border-radius: 10px;
      padding: 20px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
      z-index: 20;
      transform: translateY(20px);
      opacity: 0;
      transition: all 0.3s ease;
      pointer-events: none;
      border: 1px solid rgba(0, 170, 255, 0.3);
    }
    
    .info-card.active {
      transform: translateY(0);
      opacity: 1;
      pointer-events: all;
    }
    
    .info-card::before {
      content: '';
      position: absolute;
      top: -10px;
      left: 50%;
      transform: translateX(-50%);
      width: 0;
      height: 0;
      border-left: 10px solid transparent;
      border-right: 10px solid transparent;
      border-bottom: 10px solid rgba(0, 26, 64, 0.9);
    }
    
    .info-card img {
      width: 100%;
      height: 150px;
      object-fit: cover;
      border-radius: 5px;
      margin-bottom: 15px;
    }
    
    .tab-content {
      display: none;
    }
    
    .tab-content.active {
      display: block;
    }
    
    .qr-code {
      position: fixed;
      bottom: 20px;
      right: 20px;
      width: 100px;
      height: 100px;
      background-color: white;
      padding: 10px;
      border-radius: 10px;
      box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
      z-index: 100;
    }
    
    .qr-code img {
      width: 100%;
      height: 100%;
      object-fit: contain;
    }
    
    .ai-recommendation {
      position: fixed;
      bottom: 20px;
      left: 20px;
      max-width: 300px;
      background: rgba(0, 26, 64, 0.9);
      border-radius: 10px;
      padding: 20px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
      z-index: 100;
      border: 1px solid rgba(0, 170, 255, 0.3);
    }
    
    .snowflake {
      position: absolute;
      background: white;
      border-radius: 50%;
      opacity: 0.8;
      animation: snowfall linear infinite;
      z-index: 1;
    }
    
    @keyframes snowfall {
      0% {
        transform: translateY(-10px) rotate(0deg);
      }
      100% {
        transform: translateY(100vh) rotate(360deg);
      }
    }
    
    .loading-screen {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: #001a33;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      z-index: 9999;
      transition: opacity 0.5s ease, visibility 0.5s ease;
    }
    
    .loading-screen.hidden {
      opacity: 0;
      visibility: hidden;
    }
    
    .loading-bar {
      width: 300px;
      height: 10px;
      background: rgba(255, 255, 255, 0.2);
      border-radius: 5px;
      margin-top: 20px;
      overflow: hidden;
    }
    
    .loading-progress {
      height: 100%;
      background: linear-gradient(90deg, #0066cc, #00aaff);
      border-radius: 5px;
      width: 0;
      transition: width 0.3s ease;
    }
    
    .butterfly-animation {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 5;
      pointer-events: none;
    }
    
    .butterfly-animation canvas {
      width: 100%;
      height: 100%;
    }
  </style>
</head>
<body class="bg-gradient-ice min-h-screen">
  <!-- 加载屏幕 -->
  <div class="loading-screen">
    <h1 class="text-4xl font-bold text-white text-shadow-lg">哈尔滨冰雪大世界</h1>
    <p class="text-xl text-ice mt-4">仿生蝴蝶系统加载中...</p>
    <div class="loading-bar">
      <div class="loading-progress" id="loading-progress"></div>
    </div>
    <p class="text-sm text-ice mt-4" id="loading-status">正在初始化3D模型...</p>
  </div>

  <!-- 顶部导航栏 -->
  <nav class="fixed top-0 left-0 w-full bg-glass border-glass z-50">
    <div class="container mx-auto px-4 py-3 flex justify-between items-center">
      <div class="flex items-center">
        <img src="https://p3-flow-imagex-sign.byteimg.com/tos-cn-i-a9rns2rl98/rc/pc/super_tool/226a7b4d7d924a85b2fb6e1001ae66d4~tplv-a9rns2rl98-image.image?rcl=20251120214322200EA5F68D50D2EBDE45&rk3s=8e244e95&rrcfp=f06b921b&x-expires=1766238250&x-signature=htRG4xW5pf5acIRqUlMHhYQZaq8%3D" alt="仿生蝴蝶" class="h-10 w-10 mr-3">
        <h1 class="text-xl font-bold text-white">仿生蝴蝶 - 哈尔滨冰雪大世界</h1>
      </div>
      <div class="hidden md:flex space-x-6">
        <a href="#about" class="text-ice hover:text-white transition-colors">关于</a>
        <a href="#attractions" class="text-ice hover:text-white transition-colors">景点介绍</a>
        <a href="#culture" class="text-ice hover:text-white transition-colors">文化故事</a>
        <a href="#ai-guide" class="text-ice hover:text-white transition-colors">AI导游</a>
      </div>
      <button class="md:hidden text-ice hover:text-white" id="mobile-menu-button">
        <i class="fa fa-bars text-xl"></i>
      </button>
    </div>
    <!-- 移动端菜单 -->
    <div class="md:hidden hidden bg-glass border-glass" id="mobile-menu">
      <div class="container mx-auto px-4 py-2 flex flex-col space-y-3">
        <a href="#about" class="text-ice hover:text-white transition-colors py-2">关于</a>
        <a href="#attractions" class="text-ice hover:text-white transition-colors py-2">景点介绍</a>
        <a href="#culture" class="text-ice hover:text-white transition-colors py-2">文化故事</a>
        <a href="#ai-guide" class="text-ice hover:text-white transition-colors py-2">AI导游</a>
      </div>
    </div>
  </nav>

  <!-- 主要内容区域 -->
  <main class="pt-16">
    <!-- 3D蝴蝶展示区域 -->
    <section id="butterfly-container" class="relative h-screen flex items-center justify-center">
      <!-- 蝴蝶动画层 -->
      <div class="butterfly-animation" id="butterfly-animation"></div>
      
      <!-- 热点区域 -->
      <div class="hotspot" id="hotspot1" style="top: 40%; left: 30%;" data-target="info-card1"></div>
      <div class="hotspot" id="hotspot2" style="top: 30%; left: 50%;" data-target="info-card2"></div>
      <div class="hotspot" id="hotspot3" style="top: 50%; left: 70%;" data-target="info-card3"></div>
      <div class="hotspot" id="hotspot4" style="top: 60%; left: 40%;" data-target="info-card4"></div>
      <div class="hotspot" id="hotspot5" style="top: 45%; left: 60%;" data-target="info-card5"></div>
      
      <!-- 信息卡片 -->
      <div class="info-card" id="info-card1" style="top: 45%; left: 35%;">
        <img src="https://p3-doubao-search-sign.byteimg.com/labis/ad9b0a252506a74b4bd701c24ae2964f~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=fTTxviWhw9Khq9pCvxAcfFdI%2FMY%3D" alt="冰雪之冠主塔">
        <h3 class="text-xl font-bold text-secondary">冰雪之冠主塔</h3>
        <p class="text-ice mt-2">哈尔滨冰雪大世界的标志性建筑，用冰量近40000立方米，高度42.5米，是世界上最高的冰建筑之一。</p>
        <button class="mt-3 px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors" onclick="showTab('culture-tab')">了解文化故事</button>
      </div>
      
      <div class="info-card" id="info-card2" style="top: 35%; left: 55%;">
        <img src="https://p11-doubao-search-sign.byteimg.com/labis/54f61ee30ed6a5f2f54357289f58be6d~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=0OUB9ZN0LE50ZnHvCx7HnIRioy8%3D" alt="冰雕艺术">
        <h3 class="text-xl font-bold text-secondary">冰雕艺术</h3>
        <p class="text-ice mt-2">哈尔滨冰雪大世界汇集了世界顶尖的冰雕艺术，每年吸引来自全球的艺术家参与创作，展现冰雪艺术的魅力。</p>
        <button class="mt-3 px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors" onclick="showTab('attractions-tab')">探索更多景点</button>
      </div>
      
      <div class="info-card" id="info-card3" style="top: 55%; left: 75%;">
        <img src="https://p11-doubao-search-sign.byteimg.com/tos-cn-i-qvj2lq49k0/6c17b6e88d8e4e18a0e209e8efb4ccd7~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=MiJnHgeBtjNUye50BrL8xgJOmZ0%3D" alt="冰滑梯">
        <h3 class="text-xl font-bold text-secondary">冰滑梯</h3>
        <p class="text-ice mt-2">园区内最受欢迎的娱乐项目之一，滑道长达423米，高低落差达7层楼，让游客体验极速滑行的乐趣。</p>
        <button class="mt-3 px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors" onclick="showTab('ai-tab')">获取游览建议</button>
      </div>
      
      <div class="info-card" id="info-card4" style="top: 65%; left: 45%;">
        <img src="https://p3-doubao-search-sign.byteimg.com/labis/8a772884849a0ad3e360c57aec9b9c89~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=lPMh6AaeaaaERP5g8ybcw2LRl%2FQ%3D" alt="冰雪城堡">
        <h3 class="text-xl font-bold text-secondary">冰雪城堡</h3>
        <p class="text-ice mt-2">仿照世界各地著名建筑打造的冰雪城堡群，夜晚在灯光的映衬下如梦似幻，仿佛置身童话世界。</p>
        <button class="mt-3 px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors" onclick="showTab('culture-tab')">了解文化故事</button>
      </div>
      
      <div class="info-card" id="info-card5" style="top: 50%; left: 65%;">
        <img src="https://p11-doubao-search-sign.byteimg.com/tos-cn-i-xv4ileqgde/051fce8473714782a366d796b232c2af~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=KA8WomiHmrxkOGOY7dElGWyM%2BaM%3D" alt="冰雪夜景">
        <h3 class="text-xl font-bold text-secondary">冰雪夜景</h3>
        <p class="text-ice mt-2">夜幕降临后，整个园区被五彩斑斓的灯光照亮，冰雪建筑在灯光的映衬下呈现出绚丽多彩的景象。</p>
        <button class="mt-3 px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors" onclick="showTab('attractions-tab')">探索更多景点</button>
      </div>
      
      <!-- 蝴蝶模型容器 -->
      <div id="butterfly-model" class="absolute top-0 left-0 w-full h-full"></div>
    </section>

    <!-- 内容标签页 -->
    <section class="container mx-auto px-4 py-8">
      <!-- 关于标签页 -->
      <div id="about-tab" class="tab-content active">
        <div class="bg-glass border-glass rounded-xl p-6 mb-8">
          <h2 class="text-3xl font-bold text-secondary mb-4">关于哈尔滨冰雪大世界</h2>
          <div class="flex flex-col md:flex-row gap-6">
            <div class="md:w-1/2">
              <p class="text-ice mb-4">哈尔滨冰雪大世界始于1999年，是集冰雪艺术、冰雪建筑、冰雪体育于一体的大型冰雪主题乐园，已成功举办26届活动。2024年1月5日，被认定为世界最大冰雪主题乐园，获得吉尼斯世界纪录称号。</p>
              <p class="text-ice mb-4">园区占地面积达60万平方米，用冰量超过30万立方米，用雪量超过10万立方米，每年吸引来自世界各地的游客近130万人次。</p>
              <p class="text-ice">哈尔滨冰雪大世界不仅是展示冰雪艺术的平台，更是传播冰雪文化、促进国际文化交流的重要窗口。</p>
            </div>
            <div class="md:w-1/2">
              <img src="https://p3-doubao-search-sign.byteimg.com/tos-cn-i-be4g95zd3a/1445949891833102421~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=g7Bj2d8o5tgN7lD70wVhk%2FvAR0U%3D" alt="哈尔滨冰雪大世界" class="w-full h-64 object-cover rounded-lg">
            </div>
          </div>
        </div>
      </div>
      
      <!-- 景点介绍标签页 -->
      <div id="attractions-tab" class="tab-content">
        <h2 class="text-3xl font-bold text-secondary mb-6">主要景点介绍</h2>
        
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          <!-- 冰雪之冠主塔 -->
          <div class="bg-glass border-glass rounded-xl overflow-hidden">
            <img src="https://p3-doubao-search-sign.byteimg.com/labis/ad9b0a252506a74b4bd701c24ae2964f~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=fTTxviWhw9Khq9pCvxAcfFdI%2FMY%3D" alt="冰雪之冠主塔" class="w-full h-48 object-cover">
            <div class="p-4">
              <h3 class="text-xl font-bold text-white mb-2">冰雪之冠主塔</h3>
              <p class="text-ice">园区的标志性建筑，用冰量近40000立方米，高度42.5米。采用高难度的曲线工艺，从设计到施工都堪称冰雪建筑的奇迹。</p>
            </div>
          </div>
          
          <!-- 冰滑梯 -->
          <div class="bg-glass border-glass rounded-xl overflow-hidden">
            <img src="https://p11-doubao-search-sign.byteimg.com/labis/54f61ee30ed6a5f2f54357289f58be6d~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=0OUB9ZN0LE50ZnHvCx7HnIRioy8%3D" alt="冰滑梯" class="w-full h-48 object-cover">
            <div class="p-4">
              <h3 class="text-xl font-bold text-white mb-2">超级冰滑梯</h3>
              <p class="text-ice">滑道长达423米，高低落差达7层楼，是园区内最受欢迎的娱乐项目之一，让游客体验极速滑行的乐趣。</p>
            </div>
          </div>
          
          <!-- 冰雕艺术 -->
          <div class="bg-glass border-glass rounded-xl overflow-hidden">
            <img src="https://p11-doubao-search-sign.byteimg.com/tos-cn-i-qvj2lq49k0/6c17b6e88d8e4e18a0e209e8efb4ccd7~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=MiJnHgeBtjNUye50BrL8xgJOmZ0%3D" alt="冰雕艺术" class="w-full h-48 object-cover">
            <div class="p-4">
              <h3 class="text-xl font-bold text-white mb-2">冰雕艺术区</h3>
              <p class="text-ice">汇集了世界顶尖的冰雕艺术作品，每年举办国际冰雕比赛，展示来自全球艺术家的创意和技巧。</p>
            </div>
          </div>
          
          <!-- 冰雪城堡 -->
          <div class="bg-glass border-glass rounded-xl overflow-hidden">
            <img src="https://p3-doubao-search-sign.byteimg.com/labis/8a772884849a0ad3e360c57aec9b9c89~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=lPMh6AaeaaaERP5g8ybcw2LRl%2FQ%3D" alt="冰雪城堡" class="w-full h-48 object-cover">
            <div class="p-4">
              <h3 class="text-xl font-bold text-white mb-2">冰雪城堡群</h3>
              <p class="text-ice">仿照世界各地著名建筑打造的冰雪城堡群，包括俄罗斯冬宫、圣彼得堡教堂等，展现多元文化魅力。</p>
            </div>
          </div>
          
          <!-- 冰雪夜景 -->
          <div class="bg-glass border-glass rounded-xl overflow-hidden">
            <img src="https://p11-doubao-search-sign.byteimg.com/tos-cn-i-xv4ileqgde/051fce8473714782a366d796b232c2af~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=KA8WomiHmrxkOGOY7dElGWyM%2BaM%3D" alt="冰雪夜景" class="w-full h-48 object-cover">
            <div class="p-4">
              <h3 class="text-xl font-bold text-white mb-2">冰雪夜景</h3>
              <p class="text-ice">夜幕降临后，整个园区被五彩斑斓的灯光照亮，冰雪建筑在灯光的映衬下呈现出绚丽多彩的景象。</p>
            </div>
          </div>
          
          <!-- 冰雪活动 -->
          <div class="bg-glass border-glass rounded-xl overflow-hidden">
            <img src="https://p11-doubao-search-sign.byteimg.com/tos-cn-i-tjoges91tu/5667b5c8c737557db1e93f6528e5dc21~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=OkKtCvk7ELbk7VZOwuvfRSPvDSg%3D" alt="冰雪活动" class="w-full h-48 object-cover">
            <div class="p-4">
              <h3 class="text-xl font-bold text-white mb-2">冰雪活动</h3>
              <p class="text-ice">园区内举办各种冰雪活动，包括冰上表演、雪圈漂移、雪地摩托等，满足不同游客的娱乐需求。</p>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 文化故事标签页 -->
      <div id="culture-tab" class="tab-content">
        <h2 class="text-3xl font-bold text-secondary mb-6">冰雪文化故事</h2>
        
        <div class="bg-glass border-glass rounded-xl p-6 mb-8">
          <h3 class="text-2xl font-bold text-white mb-4">哈尔滨冰雪文化的起源</h3>
          <div class="flex flex-col md:flex-row gap-6">
            <div class="md:w-2/3">
              <p class="text-ice mb-4">哈尔滨冰雪文化的历史可以追溯到1963年，当时在哈尔滨市委第一书记任仲夷和市长吕其恩的倡导下，哈尔滨人创造了举世瞩目的现代冰灯艺术，填补了中国乃至世界冰雪造型艺术的空白。</p>
              <p class="text-ice mb-4">1985年，哈尔滨市委、市政府根据有关同志的建议创办了我国第一个冰雪节，对冰雪雕塑、冰雪体育、冰雪文艺、冰雪饮食、冰雪经贸、冰雪旅游等逐步进行了全方位的开发，哈尔滨的现代冰雪文化进入了快速发展期。</p>
              <p class="text-ice">1999年，为迎接新世纪的到来，联合国以电视实况直播形式播出各国参与的庆典活动。经过多轮评选，哈尔滨以冰雪特色代表中国参加全世界电视实况直播千年庆典活动，哈尔滨冰雪大世界应运而生。</p>
            </div>
            <div class="md:w-1/3">
              <img src="https://p3-doubao-search-sign.byteimg.com/tos-cn-i-be4g95zd3a/1767322211411034132~tplv-be4g95zd3a-image.jpeg?rk3s=542c0f93&x-expires=1779198225&x-signature=9ACvqvNtQ4Ok%2FW2TqPEKLf0u5r4%3D" alt="冰雪文化" class="w-full h-64 object-cover rounded-lg">
            </div>
          </div>
        </div>
        
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- 冰雪之冠的故事 -->
          <div class="bg-glass border-glass rounded-xl p-6">
            <h3 class="text-xl font-bold text-white mb-3">冰雪之冠的故事</h3>
            <p class="text-ice mb-3">冰雪之冠主塔以传承、创新、发展为理念，体现龙江腾飞和振兴龙江的精神。设计灵感来源于哈尔滨作为冰雪之都的荣耀与自豪，象征着哈尔滨冰雪文化的巅峰之作。</p>
            <p class="text-ice">主塔采用高难度的曲线工艺，从设计到施工都堪称冰雪建筑的奇迹。42.5米的高度不仅创造了世界纪录，更象征着哈尔滨冰雪文化的不断攀升和创新。</p>
          </div>
          
          <!-- 冰雕艺术的传承 -->
          <div class="bg-glass border-glass rounded-xl p-6">
            <h3 class="text-xl font-bold text-white mb-3">冰雕艺术的传承</h3>
            <p class="text-ice mb-3">哈尔滨冰雪大世界每年举办国际冰雕比赛，吸引来自世界各地的冰雕艺术家参与。这些艺术家们不仅展示了高超的技艺，更带来了不同国家和地区的文化特色。</p>
            <p class="text-ice">通过这些国际交流活动，哈尔滨的冰雕艺术不断吸收世界各地的艺术精华，形成了独具特色的艺术风格，成为中国冰雪文化走向世界的重要窗口。</p>
          </div>
        </div>
      </div>
      
      <!-- AI导游标签页 -->
      <div id="ai-tab" class="tab-content">
        <h2 class="text-3xl font-bold text-secondary mb-6">AI智能导游</h2>
        
        <div class="bg-glass border-glass rounded-xl p-6 mb-8">
          <h3 class="text-2xl font-bold text-white mb-4">个性化游览路线推荐</h3>
          
          <div class="mb-6">
            <label class="block text-ice mb-2">请选择您的兴趣类型：</label>
            <div class="flex flex-wrap gap-3">
              <button class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors interest-btn" data-interest="adventure">冒险刺激</button>
              <button class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors interest-btn" data-interest="culture">文化艺术</button>
              <button class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors interest-btn" data-interest="family">家庭亲子</button>
              <button class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors interest-btn" data-interest="photography">摄影打卡</button>
            </div>
          </div>
          
          <div class="mb-6">
            <label class="block text-ice mb-2">请选择您的游览时间：</label>
            <div class="flex flex-wrap gap-3">
              <button class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors time-btn" data-time="morning">上午 (10:00-12:00)</button>
              <button class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors time-btn" data-time="afternoon">下午 (12:00-16:00)</button>
              <button class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors time-btn" data-time="evening">晚上 (16:00-21:00)</button>
              <button class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors time-btn" data-time="full">全天 (10:00-21:00)</button>
            </div>
          </div>
          
          <button id="generate-route" class="px-6 py-3 bg-accent text-white rounded-full hover:bg-opacity-80 transition-colors text-lg font-bold">生成游览路线</button>
        </div>
        
        <div id="route-result" class="hidden">
          <div class="bg-glass border-glass rounded-xl p-6">
            <h3 class="text-2xl font-bold text-white mb-4">您的个性化游览路线</h3>
            
            <div class="mb-6">
              <h4 class="text-xl font-bold text-secondary mb-3">路线概览</h4>
              <p class="text-ice" id="route-overview">根据您的兴趣和时间，我们为您定制了以下游览路线，让您充分体验哈尔滨冰雪大世界的魅力。</p>
            </div>
            
            <div class="space-y-4" id="route-details">
              <!-- 路线详情将通过JavaScript动态生成 -->
            </div>
            
            <div class="mt-6">
              <button id="save-route" class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors mr-3">
                <i class="fa fa-download mr-2"></i>保存路线
              </button>
              <button id="share-route" class="px-4 py-2 bg-secondary text-white rounded-full hover:bg-opacity-80 transition-colors">
                <i class="fa fa-share-alt mr-2"></i>分享路线
              </button>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>

  <!-- 页脚 -->
  <footer class="bg-dark border-t border-glass py-6">
    <div class="container mx-auto px-4">
      <div class="flex flex-col md:flex-row justify-between items-center">
        <div class="mb-4 md:mb-0">
          <h3 class="text-xl font-bold text-white mb-2">哈尔滨冰雪大世界</h3>
          <p class="text-ice">地址：黑龙江省哈尔滨市松北区太阳岛西侧</p>
          <p class="text-ice">电话：12345678910</p>
        </div>
        <div class="flex space-x-4">
          <a href="#" class="text-ice hover:text-white transition-colors">
            <i class="fa fa-weixin text-2xl"></i>
          </a>
          <a href="#" class="text-ice hover:text-white transition-colors">
            <i class="fa fa-weibo text-2xl"></i>
          </a>
          <a href="#" class="text-ice hover:text-white transition-colors">
            <i class="fa fa-instagram text-2xl"></i>
          </a>
          <a href="#" class="text-ice hover:text-white transition-colors">
            <i class="fa fa-youtube-play text-2xl"></i>
          </a>
        </div>
      </div>
      <div class="mt-6 pt-6 border-t border-glass text-center">
        <p class="text-ice">&copy; 智翼翩跹·蝶载新语——空中灵动宣传信使——2025 哈尔滨冰雪大世界. </p>
        <p>团队成员：郭明翰、马粼梓、舒紫凝、程雅龙、张滢珂</p>
      </div>
    </div>
  </footer>

  <!-- QR码 -->
  <div class="qr-code">
    <img src="https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=https://ice-butterfly.example.com" alt="扫描二维码访问">
  </div>

  <!-- AI推荐 -->
  <div class="ai-recommendation">
    <h3 class="text-lg font-bold text-white mb-2">
      <i class="fa fa-lightbulb-o text-yellow-400 mr-2"></i>AI推荐
    </h3>
    <p class="text-ice text-sm" id="ai-recommendation-text">根据您的浏览习惯，我们推荐您参观冰雪之冠主塔和冰雕艺术区。</p>
    <button class="mt-2 text-xs text-secondary hover:text-white transition-colors" id="refresh-recommendation">
      <i class="fa fa-refresh mr-1"></i>换一个推荐
    </button>
  </div>

  <script>
    // 页面加载完成后执行
    document.addEventListener('DOMContentLoaded', function() {
      // 模拟加载进度
      simulateLoading();
      
      // 初始化3D蝴蝶模型
      initButterflyModel();
      
      // 初始化热点交互
      initHotspots();
      
      // 初始化标签页切换
      initTabs();
      
      // 初始化移动端菜单
      initMobileMenu();
      
      // 初始化AI推荐
      initAIRecommendation();
      
      // 初始化AI导游
      initAIGuide();
      
      // 创建雪花效果
      createSnowflakes();
    });
    
    // 模拟加载进度
    function simulateLoading() {
      const loadingProgress = document.getElementById('loading-progress');
      const loadingStatus = document.getElementById('loading-status');
      const loadingScreen = document.querySelector('.loading-screen');
      
      let progress = 0;
      const loadingSteps = [
        '正在初始化3D模型...',
        '正在加载景点数据...',
        '正在配置AI导游...',
        '准备就绪，欢迎使用!'
      ];
      
      const interval = setInterval(() => {
        progress += 5;
        loadingProgress.style.width = `${progress}%`;
        
        // 更新加载状态文本
        const stepIndex = Math.min(Math.floor(progress / 25), loadingSteps.length - 1);
        loadingStatus.textContent = loadingSteps[stepIndex];
        
        if (progress >= 100) {
          clearInterval(interval);
          setTimeout(() => {
            loadingScreen.classList.add('hidden');
          }, 500);
        }
      }, 100);
    }
    
    // 初始化3D蝴蝶模型
    function initButterflyModel() {
      // 创建场景
      const scene = new THREE.Scene();
      
      // 创建相机
      const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
      camera.position.z = 5;
      
      // 创建渲染器
      const renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true });
      renderer.setSize(window.innerWidth, window.innerHeight);
      renderer.setClearColor(0x000000, 0);
      
      // 添加到DOM
      const container = document.getElementById('butterfly-animation');
      container.appendChild(renderer.domElement);
      
      // 创建蝴蝶几何体
      const butterflyGeometry = new THREE.Group();
      
      // 蝴蝶身体
      const bodyGeometry = new THREE.CylinderGeometry(0.1, 0.1, 1, 32);
      const bodyMaterial = new THREE.MeshPhongMaterial({ color: 0x0066cc });
      const body = new THREE.Mesh(bodyGeometry, bodyMaterial);
      body.position.y = 0;
      butterflyGeometry.add(body);
      
      // 蝴蝶翅膀
      const wingGeometry1 = new THREE.SphereGeometry(1.5, 32, 32, 0, Math.PI, 0, Math.PI / 2);
      const wingGeometry2 = new THREE.SphereGeometry(1.5, 32, 32, 0, Math.PI, Math.PI / 2, Math.PI / 2);
      
      const wingMaterial = new THREE.MeshPhongMaterial({ 
        color: 0x00aaff, 
        transparent: true, 
        opacity: 0.7,
        side: THREE.DoubleSide
      });
      
      const wing1 = new THREE.Mesh(wingGeometry1, wingMaterial);
      const wing2 = new THREE.Mesh(wingGeometry2, wingMaterial);
      const wing3 = new THREE.Mesh(wingGeometry1, wingMaterial);
      const wing4 = new THREE.Mesh(wingGeometry2, wingMaterial);
      
      wing1.scale.set(1, 0.8, 1);
      wing2.scale.set(1, 0.8, 1);
      wing3.scale.set(1, 0.6, 1);
      wing4.scale.set(1, 0.6, 1);
      
      wing1.position.set(0.5, 0.3, 0);
      wing2.position.set(0.5, -0.3, 0);
      wing3.position.set(0.5, 0.3, 0);
      wing4.position.set(0.5, -0.3, 0);
      
      wing1.rotation.z = Math.PI / 4;
      wing2.rotation.z = -Math.PI / 4;
      wing3.rotation.z = Math.PI / 4;
      wing4.rotation.z = -Math.PI / 4;
      
      wing3.rotation.y = Math.PI;
      wing4.rotation.y = Math.PI;
      
      butterflyGeometry.add(wing1);
      butterflyGeometry.add(wing2);
      butterflyGeometry.add(wing3);
      butterflyGeometry.add(wing4);
      
      // 蝴蝶触角
      const antennaGeometry = new THREE.CylinderGeometry(0.01, 0.005, 0.5, 8);
      const antennaMaterial = new THREE.MeshPhongMaterial({ color: 0xffffff });
      
      const antenna1 = new THREE.Mesh(antennaGeometry, antennaMaterial);
      const antenna2 = new THREE.Mesh(antennaGeometry, antennaMaterial);
      
      antenna1.position.set(0, 0.5, 0);
      antenna2.position.set(0, 0.5, 0);
      
      antenna1.rotation.z = Math.PI / 6;
      antenna2.rotation.z = -Math.PI / 6;
      
      butterflyGeometry.add(antenna1);
      butterflyGeometry.add(antenna2);
      
      // 添加到场景
      scene.add(butterflyGeometry);
      
      // 添加光源
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
      scene.add(ambientLight);
      
      const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
      directionalLight.position.set(1, 1, 1);
      scene.add(directionalLight);
      
      // 添加点光源
      const pointLight1 = new THREE.PointLight(0x0066cc, 1, 10);
      const pointLight2 = new THREE.PointLight(0xff3300, 1, 10);
      
      pointLight1.position.set(2, 0, 2);
      pointLight2.position.set(-2, 0, 2);
      
      scene.add(pointLight1);
      scene.add(pointLight2);
      
      // 动画
      function animate() {
        requestAnimationFrame(animate);
        
        // 蝴蝶整体旋转
        butterflyGeometry.rotation.y += 0.01;
        
        // 翅膀扇动
        const time = Date.now() * 0.001;
        const wingFlap = Math.sin(time * 5) * 0.2;
        
        wing1.rotation.z = Math.PI / 4 + wingFlap;
        wing2.rotation.z = -Math.PI / 4 - wingFlap;
        wing3.rotation.z = Math.PI / 4 - wingFlap;
        wing4.rotation.z = -Math.PI / 4 + wingFlap;
        
        // 蝴蝶位置移动
        butterflyGeometry.position.y = Math.sin(time * 2) * 0.2;
        
        renderer.render(scene, camera);
      }
      
      // 响应窗口大小变化
      function onWindowResize() {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
      }
      
      window.addEventListener('resize', onWindowResize);
      
      // 开始动画
      animate();
    }
    
    // 初始化热点交互
    function initHotspots() {
      const hotspots = document.querySelectorAll('.hotspot');
      
      hotspots.forEach(hotspot => {
        hotspot.addEventListener('click', function() {
          const targetId = this.getAttribute('data-target');
          const infoCard = document.getElementById(targetId);
          
          // 隐藏所有信息卡片
          document.querySelectorAll('.info-card').forEach(card => {
            card.classList.remove('active');
          });
          
          // 显示目标信息卡片
          infoCard.classList.add('active');
        });
      });
      
      // 点击空白区域隐藏信息卡片
      document.addEventListener('click', function(event) {
        if (!event.target.closest('.hotspot') && !event.target.closest('.info-card')) {
          document.querySelectorAll('.info-card').forEach(card => {
            card.classList.remove('active');
          });
        }
      });
    }
    
    // 初始化标签页切换
    function initTabs() {
      const tabLinks = document.querySelectorAll('nav a');
      const tabContents = document.querySelectorAll('.tab-content');
      
      tabLinks.forEach(link => {
        link.addEventListener('click', function(e) {
          e.preventDefault();
          
          const targetId = this.getAttribute('href').substring(1);
          const targetTab = document.getElementById(`${targetId}-tab`);
          
          // 隐藏所有标签页内容
          tabContents.forEach(tab => {
            tab.classList.remove('active');
          });
          
          // 显示目标标签页内容
          targetTab.classList.add('active');
          
          // 关闭移动端菜单
          document.getElementById('mobile-menu').classList.add('hidden');
        });
      });
    }
    
    // 初始化移动端菜单
    function initMobileMenu() {
      const menuButton = document.getElementById('mobile-menu-button');
      const mobileMenu = document.getElementById('mobile-menu');
      
      menuButton.addEventListener('click', function() {
        mobileMenu.classList.toggle('hidden');
      });
    }
    
    // 初始化AI推荐
    function initAIRecommendation() {
      const refreshButton = document.getElementById('refresh-recommendation');
      const recommendationText = document.getElementById('ai-recommendation-text');
      
      const recommendations = [
        '根据您的浏览习惯，我们推荐您参观冰雪之冠主塔和冰雕艺术区。',
        '您可能会喜欢超级冰滑梯和冰雪城堡群，这是园区最受欢迎的景点。',
        '晚上的冰雪夜景非常美丽，建议您在傍晚时分前往观赏灯光秀。',
        '如果您对冰雪文化感兴趣，可以参观冰雪文化博物馆，了解哈尔滨冰雪历史。',
        '带孩子的游客可以前往儿童冰雪乐园，那里有适合小朋友的娱乐项目。'
      ];
      
      refreshButton.addEventListener('click', function() {
        const randomIndex = Math.floor(Math.random() * recommendations.length);
        recommendationText.textContent = recommendations[randomIndex];
      });
    }
    
    // 初始化AI导游
    function initAIGuide() {
      const generateButton = document.getElementById('generate-route');
      const routeResult = document.getElementById('route-result');
      const routeOverview = document.getElementById('route-overview');
      const routeDetails = document.getElementById('route-details');
      const saveButton = document.getElementById('save-route');
      const shareButton = document.getElementById('share-route');
      
      let selectedInterest = 'adventure';
      let selectedTime = 'full';
      
      // 兴趣按钮点击事件
      document.querySelectorAll('.interest-btn').forEach(btn => {
        btn.addEventListener('click', function() {
          document.querySelectorAll('.interest-btn').forEach(b => {
            b.classList.remove('bg-accent');
            b.classList.add('bg-secondary');
          });
          
          this.classList.remove('bg-secondary');
          this.classList.add('bg-accent');
          
          selectedInterest = this.getAttribute('data-interest');
        });
      });
      
      // 时间按钮点击事件
      document.querySelectorAll('.time-btn').forEach(btn => {
        btn.addEventListener('click', function() {
          document.querySelectorAll('.time-btn').forEach(b => {
            b.classList.remove('bg-accent');
            b.classList.add('bg-secondary');
          });
          
          this.classList.remove('bg-secondary');
          this.classList.add('bg-accent');
          
          selectedTime = this.getAttribute('data-time');
        });
      });
      
      // 生成路线按钮点击事件
      generateButton.addEventListener('click', function() {
        // 根据选择的兴趣和时间生成路线
        const route = generateRoute(selectedInterest, selectedTime);
        
        // 更新路线概览
        routeOverview.textContent = route.overview;
        
        // 清空路线详情
        routeDetails.innerHTML = '';
        
        // 添加路线详情
        route.details.forEach((item, index) => {
          const detailItem = document.createElement('div');
          detailItem.className = 'flex gap-4 items-start';
          detailItem.innerHTML = `
            <div class="bg-accent text-white rounded-full w-8 h-8 flex items-center justify-center flex-shrink-0">
              ${index + 1}
            </div>
            <div>
              <h5 class="font-bold text-white">${item.time} - ${item.title}</h5>
              <p class="text-ice text-sm">${item.description}</p>
            </div>
          `;
          
          routeDetails.appendChild(detailItem);
        });
        
        // 显示路线结果
        routeResult.classList.remove('hidden');
        
        // 滚动到路线结果
        routeResult.scrollIntoView({ behavior: 'smooth' });
      });
      
      // 保存路线按钮点击事件
      saveButton.addEventListener('click', function() {
        alert('路线已保存到您的账户！');
      });
      
      // 分享路线按钮点击事件
      shareButton.addEventListener('click', function() {
        alert('分享链接已复制到剪贴板！');
      });
    }
    
    // 生成游览路线
    function generateRoute(interest, time) {
      // 根据兴趣和时间生成不同的路线
      const routes = {
        adventure: {
          morning: {
            overview: '上午的冒险之旅，体验园区最刺激的项目！',
            details: [
              {
                time: '10:00-10:30',
                title: '冰雪之冠主塔',
                description: '参观园区标志性建筑，了解冰雪建筑的奇迹。'
              },
              {
                time: '10:30-11:30',
                title: '超级冰滑梯',
                description: '体验长达423米的超级冰滑梯，感受极速滑行的乐趣。'
              },
              {
                time: '11:30-12:00',
                title: '雪地摩托',
                description: '在雪地上驾驶摩托，体验速度与激情。'
              }
            ]
          },
          afternoon: {
            overview: '下午的冒险之旅，挑战更多刺激项目！',
            details: [
              {
                time: '12:00-13:00',
                title: '雪圈漂移',
                description: '乘坐雪圈在赛道上漂移，体验冰雪运动的乐趣。'
              },
              {
                time: '13:00-14:00',
                title: '冰上自行车',
                description: '在冰面上骑行特制自行车，考验平衡能力。'
              },
              {
                time: '14:00-15:00',
                title: '冰上碰碰车',
                description: '在冰面上驾驶碰碰车，享受碰撞的乐趣。'
              },
              {
                time: '15:00-16:00',
                title: '冰上芭蕾表演',
                description: '欣赏专业演员在冰面上表演优雅的芭蕾舞蹈。'
              }
            ]
          },
          evening: {
            overview: '晚上的冒险之旅，在灯光下体验冰雪魅力！',
            details: [
              {
                time: '16:00-17:00',
                title: '冰雪城堡群',
                description: '参观仿照世界各地著名建筑打造的冰雪城堡群。'
              },
              {
                time: '17:00-18:00',
                title: '冰上杂技表演',
                description: '欣赏惊险刺激的冰上杂技表演。'
              },
              {
                time: '18:00-19:00',
                title: '冰雪灯光秀',
                description: '欣赏绚丽多彩的冰雪灯光秀，感受冰雪艺术的魅力。'
              },
              {
                time: '19:00-21:00',
                title: '冰雪酒吧',
                description: '在冰雪建造的酒吧中品尝特色饮品，感受独特的冰雪氛围。'
              }
            ]
          },
          full: {
            overview: '全天的冒险之旅，体验园区所有刺激项目！',
            details: [
              {
                time: '10:00-11:00',
                title: '冰雪之冠主塔',
                description: '参观园区标志性建筑，了解冰雪建筑的奇迹。'
              },
              {
                time: '11:00-12:00',
                title: '超级冰滑梯',
                description: '体验长达423米的超级冰滑梯，感受极速滑行的乐趣。'
              },
              {
                time: '12:00-13:00',
                title: '午餐',
                description: '在园区内的餐厅享用特色美食。'
              },
              {
                time: '13:00-14:00',
                title: '雪圈漂移',
                description: '乘坐雪圈在赛道上漂移，体验冰雪运动的乐趣。'
              },
              {
                time: '14:00-15:00',
                title: '雪地摩托',
                description: '在雪地上驾驶摩托，体验速度与激情。'
              },
              {
                time: '15:00-16:00',
                title: '冰上自行车',
                description: '在冰面上骑行特制自行车，考验平衡能力。'
              },
              {
                time: '16:00-17:00',
                title: '冰雪城堡群',
                description: '参观仿照世界各地著名建筑打造的冰雪城堡群。'
              },
              {
                time: '17:00-18:00',
                title: '冰上杂技表演',
                description: '欣赏惊险刺激的冰上杂技表演。'
              },
              {
                time: '18:00-19:00',
                title: '冰雪灯光秀',
                description: '欣赏绚丽多彩的冰雪灯光秀，感受冰雪艺术的魅力。'
              },
              {
                time: '19:00-21:00',
                title: '冰雪酒吧',
                description: '在冰雪建造的酒吧中品尝特色饮品，感受独特的冰雪氛围。'
              }
            ]
          }
        },
        culture: {
          morning: {
            overview: '上午的文化之旅，了解哈尔滨冰雪文化的历史与艺术。',
            details: [
              {
                time: '10:00-11:00',
                title: '冰雪文化博物馆',
                description: '参观冰雪文化博物馆，了解哈尔滨冰雪文化的发展历史。'
              },
              {
                time: '11:00-12:00',
                title: '冰雕艺术区',
                description: '欣赏来自世界各地的冰雕艺术作品，感受冰雪艺术的魅力。'
              }
            ]
          },
          afternoon: {
            overview: '下午的文化之旅，深入体验冰雪艺术的精髓。',
            details: [
              {
                time: '12:00-13:00',
                title: '午餐',
                description: '在园区内的餐厅享用特色美食。'
              },
              {
                time: '13:00-14:00',
                title: '国际冰雕比赛展区',
                description: '参观国际冰雕比赛的获奖作品，欣赏顶尖冰雕艺术。'
              },
              {
                time: '14:00-15:00',
                title: '冰雪之冠主塔',
                description: '参观园区标志性建筑，了解冰雪建筑的奇迹。'
              },
              {
                time: '15:00-16:00',
                title: '冰雪城堡群',
                description: '参观仿照世界各地著名建筑打造的冰雪城堡群，感受多元文化魅力。'
              }
            ]
          },
          evening: {
            overview: '晚上的文化之旅，在灯光下感受冰雪艺术的魅力。',
            details: [
              {
                time: '16:00-17:00',
                title: '冰雪剧场',
                description: '观看以冰雪为主题的文艺表演，感受冰雪文化的艺术表达。'
              },
              {
                time: '17:00-18:00',
                title: '冰雪灯光秀',
                description: '欣赏绚丽多彩的冰雪灯光秀，感受冰雪艺术的魅力。'
              },
              {
                time: '18:00-19:00',
                title: '冰上芭蕾表演',
                description: '欣赏专业演员在冰面上表演优雅的芭蕾舞蹈。'
              },
              {
                time: '19:00-21:00',
                title: '冰雪民俗体验',
                description: '体验哈尔滨特色的冰雪民俗活动，感受当地文化氛围。'
              }
            ]
          },
          full: {
            overview: '全天的文化之旅，全面了解哈尔滨冰雪文化的魅力。',
            details: [
              {
                time: '10:00-11:00',
                title: '冰雪文化博物馆',
                description: '参观冰雪文化博物馆，了解哈尔滨冰雪文化的发展历史。'
              },
              {
                time: '11:00-12:00',
                title: '冰雕艺术区',
                description: '欣赏来自世界各地的冰雕艺术作品，感受冰雪艺术的魅力。'
              },
              {
                time: '12:00-13:00',
                title: '午餐',
                description: '在园区内的餐厅享用特色美食。'
              },
              {
                time: '13:00-14:00',
                title: '国际冰雕比赛展区',
                description: '参观国际冰雕比赛的获奖作品，欣赏顶尖冰雕艺术。'
              },
              {
                time: '14:00-15:00',
                title: '冰雪之冠主塔',
                description: '参观园区标志性建筑，了解冰雪建筑的奇迹。'
              },
              {
                time: '15:00-16:00',
                title: '冰雪城堡群',
                description: '参观仿照世界各地著名建筑打造的冰雪城堡群，感受多元文化魅力。'
              },
              {
                time: '16:00-17:00',
                title: '冰雪剧场',
                description: '观看以冰雪为主题的文艺表演，感受冰雪文化的艺术表达。'
              },
              {
                time: '17:00-18:00',
                title: '冰雪灯光秀',
                description: '欣赏绚丽多彩的冰雪灯光秀，感受冰雪艺术的魅力。'
              },
              {
                time: '18:00-19:00',
                title: '冰上芭蕾表演',
                description: '欣赏专业演员在冰面上表演优雅的芭蕾舞蹈。'
              },
              {
                time: '19:00-21:00',
                title: '冰雪民俗体验',
                description: '体验哈尔滨特色的冰雪民俗活动，感受当地文化氛围。'
              }
            ]
          }
        },
        family: {
          morning: {
            overview: '上午的家庭亲子之旅，适合带孩子一起体验的冰雪项目。',
            details: [
              {
                time: '10:00-11:00',
                title: '儿童冰雪乐园',
                description: '带孩子在儿童冰雪乐园玩耍，这里有适合小朋友的冰雪滑梯和游戏。'
              },
              {
                time: '11:00-12:00',
                title: '十二生肖冰雕',
                description: '参观可爱的十二生肖冰雕，让孩子找到自己的生肖并拍照留念。'
              }
            ]
          },
          afternoon: {
            overview: '下午的家庭亲子之旅，更多适合全家参与的活动。',
            details: [
              {
                time: '12:00-13:00',
                title: '午餐',
                description: '在园区内的餐厅享用特色美食，有适合儿童的餐点选择。'
              },
              {
                time: '13:00-14:00',
                title: '小型冰滑梯',
                description: '带孩子体验适合儿童的小型冰滑梯，安全又有趣。'
              },
              {
                time: '14:00-15:00',
                title: '冰雪童话城堡',
                description: '参观专为儿童设计的冰雪童话城堡，让孩子仿佛置身童话世界。'
              },
              {
                time: '15:00-16:00',
                title: '卡通人物巡游',
                description: '观看卡通人物巡游，与孩子喜欢的卡通形象合影留念。'
              }
            ]
          },
          evening: {
            overview: '晚上的家庭亲子之旅，在灯光下享受温馨的家庭时光。',
            details: [
              {
                time: '16:00-17:00',
                title: '冰雪动物世界',
                description: '参观可爱的冰雪动物造型，让孩子认识各种动物。'
              },
              {
                time: '17:00-18:00',
                title: '儿童剧表演',
                description: '观看专为儿童设计的冰雪主题儿童剧表演。'
              },
              {
                time: '18:00-19:00',
                title: '冰雪灯光秀',
                description: '欣赏绚丽多彩的冰雪灯光秀，感受冰雪艺术的魅力。'
              },
              {
                time: '19:00-21:00',
                title: '冰雪美食街',
                description: '在冰雪美食街品尝各种特色小吃，满足全家人的味蕾。'
              }
            ]
          },
          full: {
            overview: '全天的家庭亲子之旅，为全家人打造难忘的冰雪回忆。',
            details: [
              {
                time: '10:00-11:00',
                title: '儿童冰雪乐园',
                description: '带孩子在儿童冰雪乐园玩耍，这里有适合小朋友的冰雪滑梯和游戏。'
              },
              {
                time: '11:00-12:00',
                title: '十二生肖冰雕',
                description: '参观可爱的十二生肖冰雕，让孩子找到自己的生肖并拍照留念。'
              },
              {
                time: '12:00-13:00',
                title: '午餐',
                description: '在园区内的餐厅享用特色美食，有适合儿童的餐点选择。'
              },
              {
                time: '13:00-14:00',
                title: '小型冰滑梯',
                description: '带孩子体验适合儿童的小型冰滑梯，安全又有趣。'
              },
              {
                time: '14:00-15:00',
                title: '冰雪童话城堡',
                description: '参观专为儿童设计的冰雪童话城堡，让孩子仿佛置身童话世界。'
              },
              {
                time: '15:00-16:00',
                title: '卡通人物巡游',
                description: '观看卡通人物巡游，与孩子喜欢的卡通形象合影留念。'
              },
              {
                time: '16:00-17:00',
                title: '冰雪动物世界',
                description: '参观可爱的冰雪动物造型，让孩子认识各种动物。'
              },
              {
                time: '17:00-18:00',
                title: '儿童剧表演',
                description: '观看专为儿童设计的冰雪主题儿童剧表演。'
              },
              {
                time: '18:00-19:00',
                title: '冰雪灯光秀',
                description: '欣赏绚丽多彩的冰雪灯光秀，感受冰雪艺术的魅力。'
              },
              {
                time: '19:00-21:00',
                title: '冰雪美食街',
                description: '在冰雪美食街品尝各种特色小吃，满足全家人的味蕾。'
              }
            ]
          }
        },
        photography: {
          morning: {
            overview: '上午的摄影打卡之旅，捕捉冰雪世界的纯净之美。',
            details: [
              {
                time: '10:00-11:00',
                title: '冰雪之冠主塔',
                description: '拍摄园区标志性建筑，上午的光线适合拍摄建筑细节。'
              },
              {
                time: '11:00-12:00',
                title: '冰雕艺术区',
                description: '拍摄精美的冰雕艺术作品，捕捉冰雕的细节和纹理。'
              }
            ]
          },
          afternoon: {
            overview: '下午的摄影打卡之旅，利用黄金时段拍摄最美的冰雪景观。',
            details: [
              {
                time: '12:00-13:00',
                title: '午餐',
                description: '在园区内的餐厅享用特色美食。'
              },
              {
                time: '13:00-14:00',
                title: '冰雪城堡群',
                description: '拍摄仿照世界各地著名建筑打造的冰雪城堡群。'
              },
              {
                time: '14:00-15:00',
                title: '冰雪动物造型',
                description: '拍摄可爱的冰雪动物造型，捕捉它们的生动表情。'
              },
              {
                time: '15:00-16:00',
                title: '冰雪花卉',
                description: '拍摄精美的冰雪花卉造型，捕捉冰雪艺术的细腻之处。'
              }
            ]
          },
          evening: {
            overview: '晚上的摄影打卡之旅，在灯光下拍摄梦幻的冰雪夜景。',
            details: [
              {
                time: '16:00-17:00',
                title: '日落拍摄',
                description: '在最佳位置拍摄冰雪景观与日落的完美结合。'
              },
              {
                time: '17:00-18:00',
                title: '冰雪灯光秀',
                description: '拍摄绚丽多彩的冰雪灯光秀，捕捉灯光与冰雪的奇妙互动。'
              },
              {
                time: '18:00-19:00',
                title: '冰雕夜景',
                description: '拍摄灯光照射下的冰雕作品，捕捉它们在夜晚的别样魅力。'
              },
              {
                time: '19:00-21:00',
                title: '冰雪城堡夜景',
                description: '拍摄灯光照射下的冰雪城堡群，感受梦幻的童话世界。'
              }
            ]
          },
          full: {
            overview: '全天的摄影打卡之旅，捕捉冰雪世界的全方位魅力。',
            details: [
              {
                time: '10:00-11:00',
                title: '冰雪之冠主塔',
                description: '拍摄园区标志性建筑，上午的光线适合拍摄建筑细节。'
              },
              {
                time: '11:00-12:00',
                title: '冰雕艺术区',
                description: '拍摄精美的冰雕艺术作品，捕捉冰雕的细节和纹理。'
              },
              {
                time: '12:00-13:00',
                title: '午餐',
                description: '在园区内的餐厅享用特色美食。'
              },
              {
                time: '13:00-14:00',
                title: '冰雪城堡群',
                description: '拍摄仿照世界各地著名建筑打造的冰雪城堡群。'
              },
              {
                time: '14:00-15:00',
                title: '冰雪动物造型',
                description: '拍摄可爱的冰雪动物造型，捕捉它们的生动表情。'
              },
              {
                time: '15:00-16:00',
                title: '冰雪花卉',
                description: '拍摄精美的冰雪花卉造型，捕捉冰雪艺术的细腻之处。'
              },
              {
                time: '16:00-17:00',
                title: '日落拍摄',
                description: '在最佳位置拍摄冰雪景观与日落的完美结合。'
              },
              {
                time: '17:00-18:00',
                title: '冰雪灯光秀',
                description: '拍摄绚丽多彩的冰雪灯光秀，捕捉灯光与冰雪的奇妙互动。'
              },
              {
                time: '18:00-19:00',
                title: '冰雕夜景',
                description: '拍摄灯光照射下的冰雕作品，捕捉它们在夜晚的别样魅力。'
              },
              {
                time: '19:00-21:00',
                title: '冰雪城堡夜景',
                description: '拍摄灯光照射下的冰雪城堡群，感受梦幻的童话世界。'
              }
            ]
          }
        }
      };
      
      return routes[interest][time];
    }
    
    // 创建雪花效果
    function createSnowflakes() {
      const container = document.getElementById('butterfly-container');
      const snowflakeCount = 100;
      
      for (let i = 0; i < snowflakeCount; i++) {
        createSnowflake(container);
      }
    }
    
    // 创建单个雪花
    function createSnowflake(container) {
      const snowflake = document.createElement('div');
      snowflake.className = 'snowflake';
      
      // 随机大小
      const size = Math.random() * 5 + 2;
      snowflake.style.width = `${size}px`;
      snowflake.style.height = `${size}px`;
      
      // 随机位置
      const startPositionX = Math.random() * window.innerWidth;
      const startPositionY = Math.random() * -window.innerHeight;
      snowflake.style.left = `${startPositionX}px`;
      snowflake.style.top = `${startPositionY}px`;
      
      // 随机透明度
      snowflake.style.opacity = Math.random() * 0.8 + 0.2;
      
      // 随机动画持续时间
      const duration = Math.random() * 10 + 10;
      snowflake.style.animationDuration = `${duration}s`;
      
      // 随机动画延迟
      const delay = Math.random() * 10;
      snowflake.style.animationDelay = `${delay}s`;
      
      // 添加到容器
      container.appendChild(snowflake);
      
      // 动画结束后重新开始
      snowflake.addEventListener('animationend', function() {
        snowflake.style.top = `${-10}px`;
        snowflake.style.left = `${Math.random() * window.innerWidth}px`;
        
        // 重置动画
        snowflake.style.animation = 'none';
        snowflake.offsetHeight; // 触发重绘
        snowflake.style.animation = `snowfall ${duration}s linear infinite`;
      });
    }
    
    // 显示指定标签页
    function showTab(tabId) {
      // 隐藏所有标签页内容
      document.querySelectorAll('.tab-content').forEach(tab => {
        tab.classList.remove('active');
      });
      
      // 显示目标标签页内容
      document.getElementById(tabId).classList.add('active');
      
      // 滚动到标签页内容
      document.getElementById(tabId).scrollIntoView({ behavior: 'smooth' });
      
      // 隐藏所有信息卡片
      document.querySelectorAll('.info-card').forEach(card => {
        card.classList.remove('active');
      });
    }
  </script>
</body>
</html>
