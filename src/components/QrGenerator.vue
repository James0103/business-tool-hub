<template>
  <div class="qr-generator">
    <div class="generator-intro">
      <h4>📱 비즈니스 QR 코드 생성기</h4>
      <p>스토어 링크, 상품 페이지, 연락처를 QR 코드로 변환하여 고객 접근성을 높이세요!</p>
    </div>

    <div class="generator-form">
      <div class="form-section">
        <h5>QR 코드 유형 선택</h5>
        <div class="qr-type-selector">
          <button
            v-for="type in qrTypes"
            :key="type.id"
            @click="selectedType = type.id"
            :class="['type-btn', { active: selectedType === type.id }]"
          >
            <span class="type-icon">{{ type.icon }}</span>
            <span class="type-label">{{ type.label }}</span>
          </button>
        </div>
      </div>

      <div class="form-section">
        <h5>{{ getCurrentTypeLabel() }} 정보 입력</h5>

        <!-- URL 입력 (웹사이트, 스토어 링크) -->
        <div v-if="selectedType === 'url' || selectedType === 'store'" class="input-group">
          <label>{{ selectedType === 'store' ? '스토어 URL' : '웹사이트 URL' }}</label>
          <input
            v-model="qrData.url"
            type="url"
            :placeholder="selectedType === 'store' ? 'https://smartstore.naver.com/yourstore' : 'https://example.com'"
            class="text-input"
          >
        </div>

        <!-- 연락처 정보 -->
        <div v-if="selectedType === 'contact'" class="contact-form">
          <div class="input-row">
            <div class="input-group">
              <label>이름/업체명</label>
              <input v-model="qrData.name" type="text" placeholder="홍길동 또는 ○○○ 스토어" class="text-input">
            </div>
            <div class="input-group">
              <label>전화번호</label>
              <input v-model="qrData.phone" type="tel" placeholder="010-1234-5678" class="text-input">
            </div>
          </div>
          <div class="input-row">
            <div class="input-group">
              <label>이메일</label>
              <input v-model="qrData.email" type="email" placeholder="example@email.com" class="text-input">
            </div>
            <div class="input-group">
              <label>회사명 (선택)</label>
              <input v-model="qrData.company" type="text" placeholder="회사명" class="text-input">
            </div>
          </div>
        </div>

        <!-- 텍스트 입력 -->
        <div v-if="selectedType === 'text'" class="input-group">
          <label>텍스트 내용</label>
          <textarea
            v-model="qrData.text"
            placeholder="QR 코드로 변환할 텍스트를 입력하세요"
            class="textarea-input"
            rows="4"
          ></textarea>
        </div>

        <!-- WiFi 정보 -->
        <div v-if="selectedType === 'wifi'" class="wifi-form">
          <div class="input-row">
            <div class="input-group">
              <label>네트워크 이름 (SSID)</label>
              <input v-model="qrData.ssid" type="text" placeholder="WiFi 네트워크 이름" class="text-input">
            </div>
            <div class="input-group">
              <label>비밀번호</label>
              <input v-model="qrData.password" type="text" placeholder="WiFi 비밀번호" class="text-input">
            </div>
          </div>
          <div class="input-group">
            <label>보안 타입</label>
            <select v-model="qrData.security" class="select-input">
              <option value="WPA">WPA/WPA2</option>
              <option value="WEP">WEP</option>
              <option value="">암호화 없음</option>
            </select>
          </div>
        </div>
      </div>

      <div class="form-section">
        <h5>QR 코드 설정</h5>
        <div class="settings-grid">
          <div class="setting-item">
            <label>크기</label>
            <select v-model="qrSettings.size" class="select-input">
              <option value="200">소형 (200x200)</option>
              <option value="300">중형 (300x300)</option>
              <option value="400">대형 (400x400)</option>
            </select>
          </div>
          <div class="setting-item">
            <label>오류 복구 레벨</label>
            <select v-model="qrSettings.errorLevel" class="select-input">
              <option value="L">낮음 (7%)</option>
              <option value="M">중간 (15%)</option>
              <option value="Q">높음 (25%)</option>
              <option value="H">최고 (30%)</option>
            </select>
          </div>
        </div>
      </div>

      <button @click="generateQR" :disabled="!canGenerate" class="generate-btn">
        QR 코드 생성하기
      </button>
    </div>

    <div v-if="generatedQR" class="qr-result">
      <h5>생성된 QR 코드</h5>
      <div class="qr-display">
        <div class="qr-code">
          <div class="qr-placeholder" :style="{ width: qrSettings.size + 'px', height: qrSettings.size + 'px' }">
            <div class="qr-pattern">
              <div class="qr-corner top-left"></div>
              <div class="qr-corner top-right"></div>
              <div class="qr-corner bottom-left"></div>
              <div class="qr-dots">
                <div v-for="i in 100" :key="i" class="qr-dot" :style="getRandomDotStyle()"></div>
              </div>
            </div>
          </div>
        </div>
        <div class="qr-info">
          <h6>{{ getCurrentTypeLabel() }}</h6>
          <p class="qr-content">{{ getQRContent() }}</p>
          <div class="qr-actions">
            <button @click="downloadQR" class="action-btn primary">
              📥 다운로드
            </button>
            <button @click="copyQRLink" class="action-btn">
              📋 링크 복사
            </button>
            <button @click="printQR" class="action-btn">
              🖨️ 인쇄
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="usage-tips">
      <h5>💡 활용 팁</h5>
      <div class="tips-grid">
        <div class="tip-item">
          <div class="tip-icon">🏪</div>
          <div class="tip-text">
            <strong>스토어 홍보</strong>
            <p>명함이나 전단지에 QR 코드를 넣어 고객이 쉽게 스토어에 접근할 수 있도록 하세요</p>
          </div>
        </div>
        <div class="tip-item">
          <div class="tip-icon">📦</div>
          <div class="tip-text">
            <strong>상품 정보</strong>
            <p>상품 포장이나 태그에 QR 코드를 부착하여 상세 정보나 사용법을 제공하세요</p>
          </div>
        </div>
        <div class="tip-item">
          <div class="tip-icon">📞</div>
          <div class="tip-text">
            <strong>고객 서비스</strong>
            <p>연락처 QR 코드로 고객이 빠르게 문의할 수 있는 환경을 만드세요</p>
          </div>
        </div>
        <div class="tip-item">
          <div class="tip-icon">📊</div>
          <div class="tip-text">
            <strong>리뷰 요청</strong>
            <p>리뷰 페이지 QR 코드를 배송 상품에 동봉하여 고객 후기를 늘려보세요</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: 'QrGenerator',
  setup() {
    const selectedType = ref('url')
    const generatedQR = ref(false)

    const qrTypes = [
      { id: 'url', label: '웹사이트', icon: '🌐' },
      { id: 'store', label: '스토어 링크', icon: '🏪' },
      { id: 'contact', label: '연락처', icon: '📇' },
      { id: 'text', label: '텍스트', icon: '📝' },
      { id: 'wifi', label: 'WiFi', icon: '📶' }
    ]

    const qrData = ref({
      url: '',
      name: '',
      phone: '',
      email: '',
      company: '',
      text: '',
      ssid: '',
      password: '',
      security: 'WPA'
    })

    const qrSettings = ref({
      size: '300',
      errorLevel: 'M'
    })

    const getCurrentTypeLabel = () => {
      const type = qrTypes.find(t => t.id === selectedType.value)
      return type ? type.label : ''
    }

    const canGenerate = computed(() => {
      switch (selectedType.value) {
        case 'url':
        case 'store':
          return qrData.value.url.trim() !== ''
        case 'contact':
          return qrData.value.name.trim() !== '' || qrData.value.phone.trim() !== ''
        case 'text':
          return qrData.value.text.trim() !== ''
        case 'wifi':
          return qrData.value.ssid.trim() !== ''
        default:
          return false
      }
    })

    const generateQR = () => {
      if (canGenerate.value) {
        generatedQR.value = true
      }
    }

    const getQRContent = () => {
      switch (selectedType.value) {
        case 'url':
        case 'store':
          return qrData.value.url
        case 'contact':
          return `${qrData.value.name} | ${qrData.value.phone}`
        case 'text':
          return qrData.value.text.substring(0, 50) + (qrData.value.text.length > 50 ? '...' : '')
        case 'wifi':
          return `WiFi: ${qrData.value.ssid}`
        default:
          return ''
      }
    }

    const getRandomDotStyle = () => {
      return {
        left: Math.random() * 90 + '%',
        top: Math.random() * 90 + '%',
        transform: `rotate(${Math.random() * 360}deg)`
      }
    }

    const downloadQR = () => {
      alert('QR 코드가 다운로드됩니다. (실제 구현에서는 이미지 파일로 저장)')
    }

    const copyQRLink = () => {
      navigator.clipboard.writeText(getQRContent())
      alert('QR 코드 내용이 클립보드에 복사되었습니다!')
    }

    const printQR = () => {
      window.print()
    }

    return {
      selectedType,
      qrTypes,
      qrData,
      qrSettings,
      generatedQR,
      getCurrentTypeLabel,
      canGenerate,
      generateQR,
      getQRContent,
      getRandomDotStyle,
      downloadQR,
      copyQRLink,
      printQR
    }
  }
}
</script>

<style scoped>
.qr-generator {
  max-width: 100%;
}

.generator-intro {
  text-align: center;
  margin-bottom: 30px;
  padding: 20px;
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  border-radius: 12px;
  color: white;
}

.generator-intro h4 {
  font-size: 1.5rem;
  margin-bottom: 10px;
}

.generator-form {
  margin-bottom: 30px;
}

.form-section {
  margin-bottom: 30px;
}

.form-section h5 {
  color: #2d3748;
  margin-bottom: 20px;
  font-size: 1.1rem;
}

.qr-type-selector {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  gap: 15px;
  margin-bottom: 20px;
}

.type-btn {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  padding: 20px 15px;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  background: white;
  cursor: pointer;
  transition: all 0.3s ease;
}

.type-btn:hover {
  border-color: #10b981;
  background: #f0fdf4;
}

.type-btn.active {
  border-color: #10b981;
  background: #10b981;
  color: white;
}

.type-icon {
  font-size: 1.5rem;
}

.type-label {
  font-size: 0.9rem;
  font-weight: 500;
}

.input-group {
  margin-bottom: 20px;
}

.input-group label {
  display: block;
  color: #374151;
  font-weight: 500;
  margin-bottom: 8px;
}

.text-input, .textarea-input, .select-input {
  width: 100%;
  padding: 12px 16px;
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.text-input:focus, .textarea-input:focus, .select-input:focus {
  outline: none;
  border-color: #10b981;
}

.textarea-input {
  resize: vertical;
  min-height: 100px;
}

.contact-form, .wifi-form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.input-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.settings-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
}

.generate-btn {
  width: 100%;
  background: #10b981;
  color: white;
  border: none;
  padding: 16px 24px;
  border-radius: 12px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.3s ease;
}

.generate-btn:hover:not(:disabled) {
  background: #059669;
}

.generate-btn:disabled {
  background: #d1d5db;
  cursor: not-allowed;
}

.qr-result {
  background: #f8fafc;
  padding: 30px;
  border-radius: 12px;
  margin-bottom: 30px;
}

.qr-display {
  display: grid;
  grid-template-columns: auto 1fr;
  gap: 30px;
  align-items: start;
  margin-top: 20px;
}

.qr-code {
  display: flex;
  justify-content: center;
}

.qr-placeholder {
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  background: white;
  position: relative;
  overflow: hidden;
}

.qr-pattern {
  width: 100%;
  height: 100%;
  position: relative;
}

.qr-corner {
  position: absolute;
  width: 20%;
  height: 20%;
  border: 3px solid #1f2937;
  border-radius: 2px;
}

.qr-corner.top-left {
  top: 10%;
  left: 10%;
}

.qr-corner.top-right {
  top: 10%;
  right: 10%;
}

.qr-corner.bottom-left {
  bottom: 10%;
  left: 10%;
}

.qr-corner::after {
  content: '';
  position: absolute;
  top: 30%;
  left: 30%;
  width: 40%;
  height: 40%;
  background: #1f2937;
  border-radius: 1px;
}

.qr-dots {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.qr-dot {
  position: absolute;
  width: 3px;
  height: 3px;
  background: #1f2937;
  border-radius: 1px;
}

.qr-info h6 {
  color: #1f2937;
  font-size: 1.2rem;
  margin-bottom: 10px;
}

.qr-content {
  color: #6b7280;
  font-size: 0.9rem;
  margin-bottom: 20px;
  word-break: break-all;
}

.qr-actions {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.action-btn {
  padding: 10px 16px;
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  background: white;
  cursor: pointer;
  font-size: 0.9rem;
  transition: all 0.3s ease;
}

.action-btn.primary {
  background: #10b981;
  color: white;
  border-color: #10b981;
}

.action-btn:hover {
  border-color: #10b981;
  background: #f0fdf4;
}

.action-btn.primary:hover {
  background: #059669;
}

.usage-tips {
  background: #f0f9ff;
  padding: 25px;
  border-radius: 12px;
  border-left: 4px solid #0ea5e9;
}

.tips-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 20px;
  margin-top: 20px;
}

.tip-item {
  display: flex;
  gap: 15px;
  align-items: flex-start;
}

.tip-icon {
  font-size: 2rem;
  flex-shrink: 0;
}

.tip-text strong {
  display: block;
  color: #1e293b;
  margin-bottom: 5px;
}

.tip-text p {
  color: #64748b;
  font-size: 0.9rem;
  line-height: 1.4;
}

@media (max-width: 768px) {
  .qr-type-selector {
    grid-template-columns: repeat(2, 1fr);
  }

  .input-row {
    grid-template-columns: 1fr;
  }

  .qr-display {
    grid-template-columns: 1fr;
    gap: 20px;
  }

  .qr-code {
    justify-self: center;
  }

  .qr-actions {
    justify-content: center;
  }

  .tips-grid {
    grid-template-columns: 1fr;
  }
}
</style>
