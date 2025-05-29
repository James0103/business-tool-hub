<script>
import { ref } from 'vue'
import PriceTracker from './components/PriceTracker.vue'
import QrGenerator from './components/QrGenerator.vue'

export default {
  name: 'App',
  components: {
    PriceTracker,
    QrGenerator
  },
  setup() {
    const selectedTool = ref(null)

    const selectTool = (tool) => {
      selectedTool.value = tool
    }

    const getToolTitle = (tool) => {
      const titles = {
        'price-tracker': '💰 가격 추적기',
        'qr-generator': '📱 QR 코드 생성기'
      }
      return titles[tool] || ''
    }

    return {
      selectedTool,
      selectTool,
      getToolTitle
    }
  }
}
</script>

<template>
  <div id="app">
    <header class="header">
      <div class="container">
        <h1 class="logo">🛠️ BusinessToolHub</h1>
        <p class="tagline">필요한 비즈니스 도구를 편의점처럼 간편하게</p>
      </div>
    </header>

    <main class="main">
      <div class="container">
        <div class="hero-section">
          <h2>네이버 스마트 스토어 운영자를 위한 올인원 도구</h2>
          <p>비즈니스 성장에 필요한 모든 도구를 한 곳에서</p>
        </div>

        <div class="tools-kanban">
          <div class="kanban-column">
            <h3 class="column-title">📊 마케팅 도구</h3>
            <div class="tool-cards">
              <div class="tool-card" @click="selectTool('price-tracker')">
                <div class="card-icon">💰</div>
                <h4>가격 추적기</h4>
                <p>키워드별 가격 변동을 10분마다 모니터링하고 리포트를 제공합니다</p>
                <div class="card-status">🔥 인기</div>
              </div>
            </div>
          </div>

          <div class="kanban-column">
            <h3 class="column-title">🔧 유틸리티</h3>
            <div class="tool-cards">
              <div class="tool-card" @click="selectTool('qr-generator')">
                <div class="card-icon">📱</div>
                <h4>QR 코드 생성기</h4>
                <p>스토어 링크, 상품 페이지를 QR 코드로 쉽게 변환하세요</p>
                <div class="card-status">✨ 신규</div>
              </div>
            </div>
          </div>

          <div class="kanban-column">
            <h3 class="column-title">📈 곧 출시</h3>
            <div class="tool-cards">
              <div class="tool-card coming-soon">
                <div class="card-icon">📊</div>
                <h4>매출 분석기</h4>
                <p>스마트 스토어 매출 데이터를 시각화합니다</p>
                <div class="card-status">🚀 개발중</div>
              </div>
              <div class="tool-card coming-soon">
                <div class="card-icon">📝</div>
                <h4>상품 설명 생성기</h4>
                <p>AI로 매력적인 상품 설명을 자동 생성합니다</p>
                <div class="card-status">🚀 개발중</div>
              </div>
            </div>
          </div>
        </div>

        <!-- 도구 상세 영역 -->
        <div v-if="selectedTool" class="tool-detail">
          <div class="tool-header">
            <button class="back-btn" @click="selectedTool = null">← 돌아가기</button>
            <h3>{{ getToolTitle(selectedTool) }}</h3>
          </div>

          <component :is="selectedTool" />
        </div>
      </div>
    </main>

    <footer class="footer">
      <div class="container">
        <p>&copy; 2024 BusinessToolHub. 소상공인의 성공을 응원합니다.</p>
      </div>
    </footer>
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

/* Header */
.header {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
  padding: 20px 0;
}

.logo {
  font-size: 2.5rem;
  font-weight: 700;
  color: white;
  margin-bottom: 8px;
}

.tagline {
  color: rgba(255, 255, 255, 0.9);
  font-size: 1.1rem;
}

/* Main */
.main {
  padding: 40px 0;
}

.hero-section {
  text-align: center;
  margin-bottom: 50px;
  color: white;
}

.hero-section h2 {
  font-size: 2.2rem;
  margin-bottom: 15px;
  font-weight: 600;
}

.hero-section p {
  font-size: 1.2rem;
  opacity: 0.9;
}

/* Kanban Board */
.tools-kanban {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 30px;
  margin-bottom: 40px;
}

.kanban-column {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 25px;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.column-title {
  font-size: 1.3rem;
  font-weight: 600;
  color: white;
  margin-bottom: 20px;
  text-align: center;
}

.tool-cards {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.tool-card {
  background: white;
  border-radius: 12px;
  padding: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  position: relative;
}

.tool-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 30px rgba(0, 0, 0, 0.2);
}

.tool-card.coming-soon {
  opacity: 0.7;
  cursor: not-allowed;
}

.tool-card.coming-soon:hover {
  transform: none;
}

.card-icon {
  font-size: 2.5rem;
  margin-bottom: 15px;
}

.tool-card h4 {
  font-size: 1.3rem;
  font-weight: 600;
  margin-bottom: 10px;
  color: #2d3748;
}

.tool-card p {
  color: #718096;
  line-height: 1.5;
  margin-bottom: 15px;
}

.card-status {
  position: absolute;
  top: 15px;
  right: 15px;
  font-size: 0.8rem;
  padding: 4px 8px;
  border-radius: 20px;
  background: #f0f8ff;
  color: #1e40af;
  font-weight: 500;
}

/* Tool Detail */
.tool-detail {
  background: white;
  border-radius: 16px;
  padding: 30px;
  margin-top: 40px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
}

.tool-header {
  display: flex;
  align-items: center;
  gap: 20px;
  margin-bottom: 30px;
  padding-bottom: 20px;
  border-bottom: 2px solid #f1f5f9;
}

.back-btn {
  background: #6366f1;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 500;
  transition: background 0.3s ease;
}

.back-btn:hover {
  background: #4f46e5;
}

.tool-header h3 {
  font-size: 1.8rem;
  color: #1e293b;
}

/* Footer */
.footer {
  background: rgba(0, 0, 0, 0.2);
  color: rgba(255, 255, 255, 0.8);
  text-align: center;
  padding: 20px 0;
  margin-top: 60px;
}

/* Responsive */
@media (max-width: 768px) {
  .tools-kanban {
    grid-template-columns: 1fr;
  }

  .logo {
    font-size: 2rem;
  }

  .hero-section h2 {
    font-size: 1.8rem;
  }

  .tool-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 15px;
  }
}
</style>
