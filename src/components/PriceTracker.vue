<template>
  <div class="price-tracker">
    <div class="tracker-intro">
      <h4>💰 스마트 가격 추적기</h4>
      <p>키워드별 가격 변동을 실시간으로 모니터링하고, 경쟁사 대비 우위를 확보하세요!</p>
    </div>

    <div class="tracker-form">
      <div class="form-section">
        <h5>추적할 키워드 설정</h5>
        <div class="input-group">
          <input
            v-model="newKeyword"
            type="text"
            placeholder="상품 키워드를 입력하세요 (예: 무선이어폰)"
            class="keyword-input"
            @keyup.enter="addKeyword"
          >
          <button @click="addKeyword" class="add-btn" :disabled="!newKeyword.trim()">
            추가
          </button>
        </div>
      </div>

      <div class="keywords-list" v-if="keywords.length > 0">
        <h5>추적 중인 키워드 ({{ keywords.length }}개)</h5>
        <div class="keyword-tags">
          <div v-for="(keyword, index) in keywords" :key="index" class="keyword-tag">
            <span>{{ keyword.name }}</span>
            <span class="tracking-status">📊 추적중</span>
            <button @click="removeKeyword(index)" class="remove-btn">×</button>
          </div>
        </div>
      </div>
    </div>

    <div class="tracker-settings">
      <h5>모니터링 설정</h5>
      <div class="settings-grid">
        <div class="setting-item">
          <label>추적 간격</label>
          <select v-model="trackingInterval" class="select-input">
            <option value="10">10분마다</option>
            <option value="30">30분마다</option>
            <option value="60">1시간마다</option>
          </select>
        </div>
        <div class="setting-item">
          <label>가격 변동 알림</label>
          <div class="checkbox-group">
            <label class="checkbox-label">
              <input type="checkbox" v-model="notifications.priceChange">
              가격 변동시 알림
            </label>
            <label class="checkbox-label">
              <input type="checkbox" v-model="notifications.dailyReport">
              일일 리포트
            </label>
          </div>
        </div>
      </div>
    </div>

    <div class="demo-results" v-if="keywords.length > 0">
      <h5>가격 추적 결과 (데모)</h5>
      <div class="results-grid">
        <div v-for="(keyword, index) in keywords" :key="index" class="result-card">
          <h6>{{ keyword.name }}</h6>
          <div class="price-info">
            <div class="current-price">
              <span class="label">현재 최저가</span>
              <span class="price">{{ generateRandomPrice() }}원</span>
            </div>
            <div class="price-change">
              <span class="change-indicator" :class="getRandomChange().type">
                {{ getRandomChange().text }}
              </span>
            </div>
          </div>
          <div class="competitors">
            <div class="competitor">
              <span>쿠팡: {{ generateRandomPrice() }}원</span>
            </div>
            <div class="competitor">
              <span>11번가: {{ generateRandomPrice() }}원</span>
            </div>
            <div class="competitor">
              <span>옥션: {{ generateRandomPrice() }}원</span>
            </div>
          </div>
          <div class="last-updated">
            마지막 업데이트: {{ new Date().toLocaleTimeString() }}
          </div>
        </div>
      </div>
    </div>

    <div class="feature-info">
      <h5>🚀 주요 기능</h5>
      <div class="features-grid">
        <div class="feature-item">
          <div class="feature-icon">📊</div>
          <div class="feature-text">
            <strong>실시간 모니터링</strong>
            <p>10분마다 주요 쇼핑몰 가격 확인</p>
          </div>
        </div>
        <div class="feature-item">
          <div class="feature-icon">📱</div>
          <div class="feature-text">
            <strong>즉시 알림</strong>
            <p>가격 변동시 카카오톡/SMS 알림</p>
          </div>
        </div>
        <div class="feature-item">
          <div class="feature-icon">📈</div>
          <div class="feature-text">
            <strong>트렌드 분석</strong>
            <p>일/주/월별 가격 변동 리포트</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  name: 'PriceTracker',
  setup() {
    const newKeyword = ref('')
    const keywords = ref([])
    const trackingInterval = ref('10')
    const notifications = ref({
      priceChange: true,
      dailyReport: false
    })

    const addKeyword = () => {
      if (newKeyword.value.trim()) {
        keywords.value.push({
          name: newKeyword.value.trim(),
          addedAt: new Date()
        })
        newKeyword.value = ''
      }
    }

    const removeKeyword = (index) => {
      keywords.value.splice(index, 1)
    }

    const generateRandomPrice = () => {
      return (Math.floor(Math.random() * 50000) + 10000).toLocaleString()
    }

    const getRandomChange = () => {
      const changes = [
        { type: 'increase', text: '↗ +3.2%' },
        { type: 'decrease', text: '↘ -1.8%' },
        { type: 'neutral', text: '→ 0.0%' }
      ]
      return changes[Math.floor(Math.random() * changes.length)]
    }

    return {
      newKeyword,
      keywords,
      trackingInterval,
      notifications,
      addKeyword,
      removeKeyword,
      generateRandomPrice,
      getRandomChange
    }
  }
}
</script>

<style scoped>
.price-tracker {
  max-width: 100%;
}

.tracker-intro {
  text-align: center;
  margin-bottom: 30px;
  padding: 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 12px;
  color: white;
}

.tracker-intro h4 {
  font-size: 1.5rem;
  margin-bottom: 10px;
}

.tracker-form {
  margin-bottom: 30px;
}

.form-section {
  margin-bottom: 25px;
}

.form-section h5 {
  color: #2d3748;
  margin-bottom: 15px;
  font-size: 1.1rem;
}

.input-group {
  display: flex;
  gap: 10px;
}

.keyword-input {
  flex: 1;
  padding: 12px 16px;
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.keyword-input:focus {
  outline: none;
  border-color: #6366f1;
}

.add-btn {
  background: #10b981;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 500;
  transition: background 0.3s ease;
}

.add-btn:hover:not(:disabled) {
  background: #059669;
}

.add-btn:disabled {
  background: #d1d5db;
  cursor: not-allowed;
}

.keywords-list {
  background: #f8fafc;
  padding: 20px;
  border-radius: 8px;
  margin-top: 20px;
}

.keyword-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 15px;
}

.keyword-tag {
  display: flex;
  align-items: center;
  gap: 8px;
  background: white;
  padding: 8px 12px;
  border-radius: 20px;
  border: 1px solid #e2e8f0;
  font-size: 0.9rem;
}

.tracking-status {
  color: #059669;
  font-size: 0.8rem;
}

.remove-btn {
  background: #ef4444;
  color: white;
  border: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  cursor: pointer;
  font-size: 0.8rem;
  display: flex;
  align-items: center;
  justify-content: center;
}

.tracker-settings {
  background: #f8fafc;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 30px;
}

.settings-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-top: 15px;
}

.setting-item label {
  display: block;
  color: #374151;
  font-weight: 500;
  margin-bottom: 8px;
}

.select-input {
  width: 100%;
  padding: 10px;
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  background: white;
}

.checkbox-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 8px;
  font-weight: normal;
  margin-bottom: 0;
}

.demo-results {
  margin-bottom: 30px;
}

.results-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
  margin-top: 15px;
}

.result-card {
  background: white;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.result-card h6 {
  color: #1f2937;
  margin-bottom: 15px;
  font-size: 1.1rem;
}

.price-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
  padding-bottom: 15px;
  border-bottom: 1px solid #f1f5f9;
}

.current-price .label {
  display: block;
  color: #6b7280;
  font-size: 0.9rem;
  margin-bottom: 4px;
}

.current-price .price {
  color: #1f2937;
  font-size: 1.3rem;
  font-weight: 600;
}

.change-indicator {
  font-weight: 600;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 0.9rem;
}

.change-indicator.increase {
  background: #fee2e2;
  color: #dc2626;
}

.change-indicator.decrease {
  background: #dcfce7;
  color: #16a34a;
}

.change-indicator.neutral {
  background: #f3f4f6;
  color: #6b7280;
}

.competitors {
  margin-bottom: 15px;
}

.competitor {
  padding: 5px 0;
  color: #6b7280;
  font-size: 0.9rem;
}

.last-updated {
  color: #9ca3af;
  font-size: 0.8rem;
  text-align: right;
}

.feature-info {
  background: #f0f9ff;
  padding: 20px;
  border-radius: 8px;
  border-left: 4px solid #0ea5e9;
}

.features-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-top: 15px;
}

.feature-item {
  display: flex;
  gap: 15px;
  align-items: flex-start;
}

.feature-icon {
  font-size: 2rem;
  flex-shrink: 0;
}

.feature-text strong {
  display: block;
  color: #1e293b;
  margin-bottom: 5px;
}

.feature-text p {
  color: #64748b;
  font-size: 0.9rem;
  line-height: 1.4;
}

@media (max-width: 768px) {
  .input-group {
    flex-direction: column;
  }

  .price-info {
    flex-direction: column;
    align-items: flex-start;
    gap: 10px;
  }

  .settings-grid,
  .features-grid,
  .results-grid {
    grid-template-columns: 1fr;
  }
}
</style>
