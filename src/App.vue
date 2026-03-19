<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const features = [
  {
    title: '自动诊断',
    desc: '接收 Prometheus 告警后，自动分析服务状态、日志线索和上下文信息，快速定位常见故障原因。',
  },
  {
    title: '自动修复',
    desc: '根据预定义场景与执行协议，自动生成并执行修复步骤，减少人工重复操作。',
  },
  {
    title: '自动验证',
    desc: '执行完成后自动校验服务是否恢复，避免“已经执行但业务仍未恢复”的情况。',
  },
  {
    title: '过程可审计',
    desc: '保留诊断、计划、执行、验证全流程记录，方便复盘、交付和后续治理。',
  },
]

const scenarios = [
  'Web 服务异常自动恢复（如 Nginx）',
  '数据库服务异常自动恢复（如 MySQL）',
  'Kubernetes Pod CrashLoop 自动恢复',
  '常见服务不可用告警自动处理',
]

const faqs = [
  {
    q: '需要替换现有监控系统吗？',
    a: '不需要。当前方案直接对接 Prometheus / Alertmanager，尽量不改你现有监控体系。',
  },
  {
    q: '支持 Kubernetes 环境吗？',
    a: '支持，适合已经使用 Kubernetes、Prometheus、Alertmanager 的技术团队。',
  },
  {
    q: '这是卖源码还是卖服务？',
    a: '当前阶段以部署服务、定制服务和后续运维支持为主，重点是帮助客户落地自动修复能力。',
  },
  {
    q: '是否支持企业付款？',
    a: '支持微信、支付宝、对公转账，也支持签合同和企业付款流程。',
  },
]

const demoSteps = [
  '接收告警',
  '自动分析',
  '匹配场景',
  '生成处理步骤',
  '执行修复',
  '验证恢复',
]

const demoTimeline = [
  {
    step: '接收告警',
    diagnosis: '-',
    action: '-',
    recovery: '待处理',
    result: '处理中',
    logs: [
      '[ALERT] nginx process down',
      '[SYSTEM] 已接收 Prometheus 告警',
    ],
  },
  {
    step: '自动分析',
    diagnosis: 'nginx service stopped',
    action: '-',
    recovery: '待处理',
    result: '处理中',
    logs: [
      '[AI] analyzing service status...',
      '[AI] detected: nginx service stopped',
    ],
  },
  {
    step: '匹配场景',
    diagnosis: 'nginx service stopped',
    action: '-',
    recovery: '待处理',
    result: '处理中',
    logs: [
      '[AI] matching incident scenario...',
      '[AI] matched: nginx service stopped recovery flow',
    ],
  },
  {
    step: '生成处理步骤',
    diagnosis: 'nginx service stopped',
    action: 'restart nginx',
    recovery: '待处理',
    result: '处理中',
    logs: [
      '[PLAN] generate repair steps',
      '[PLAN] action prepared: restart nginx',
    ],
  },
  {
    step: '执行修复',
    diagnosis: 'nginx service stopped',
    action: 'restart nginx',
    recovery: '验证中',
    result: '处理中',
    logs: [
      '[EXEC] restarting nginx...',
      '[EXEC] restart command completed',
    ],
  },
  {
    step: '验证恢复',
    diagnosis: 'nginx service stopped',
    action: 'verify port 80',
    recovery: '已恢复',
    result: '告警已清除',
    logs: [
      '[VERIFY] checking port 80...',
      '[OK] port 80 reachable',
      '[DONE] alert recovered',
    ],
  },
]

const demoCurrentStep = ref(-1)
const demoPlaying = ref(false)
const demoTimer = ref(null)

const demoState = ref({
  alert: 'nginx process down',
  service: 'nginx-prod',
  step: '-',
  diagnosis: '-',
  action: '-',
  recovery: '-',
  result: '-',
})

const demoLogs = ref([
  '[DEMO] 点击“播放演示”查看一次典型自动修复链路',
])

const activeSection = ref('hero')

const sectionIds = [
  'hero',
  'solution',
  'architecture',
  'demo',
  'pricing',
  'contact',
]

const isDemoFinished = computed(() => demoCurrentStep.value === demoSteps.length - 1)

function resetDemoState() {
  demoCurrentStep.value = -1
  demoState.value = {
    alert: 'nginx process down',
    service: 'nginx-prod',
    step: '-',
    diagnosis: '-',
    action: '-',
    recovery: '-',
    result: '-',
  }
  demoLogs.value = [
    '[DEMO] 点击“播放演示”查看一次典型自动修复链路',
  ]
}

function stopDemoTimer() {
  if (demoTimer.value) {
    clearInterval(demoTimer.value)
    demoTimer.value = null
  }
}

function applyDemoFrame(index) {
  const frame = demoTimeline[index]
  demoState.value = {
    alert: 'nginx process down',
    service: 'nginx-prod',
    step: frame.step,
    diagnosis: frame.diagnosis,
    action: frame.action,
    recovery: frame.recovery,
    result: frame.result,
  }
  demoLogs.value = frame.logs
}

function playDemo() {
  stopDemoTimer()
  resetDemoState()
  demoPlaying.value = true

  demoTimer.value = setInterval(() => {
    demoCurrentStep.value += 1
    applyDemoFrame(demoCurrentStep.value)

    if (demoCurrentStep.value >= demoSteps.length - 1) {
      stopDemoTimer()
      demoPlaying.value = false
    }
  }, 1500)
}

function handleScroll() {
  const scrollY = window.scrollY + 120

  for (const id of sectionIds) {
    const el = document.getElementById(id)
    if (!el) continue

    const top = el.offsetTop
    const height = el.offsetHeight

    if (scrollY >= top && scrollY < top + height) {
      activeSection.value = id
      break
    }
  }
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll, { passive: true })
  handleScroll()
})

onBeforeUnmount(() => {
  stopDemoTimer()
  window.removeEventListener('scroll', handleScroll)
})
</script>

<template>
  <main class="page">
    <!-- NAV -->
    <header class="nav">
      <div class="container nav-inner">
        <a class="brand" href="#hero" aria-label="AI Ops Agent">
          <div class="brand-logo">
            <img src="/logo/logo.png" alt="AI Ops Agent Logo" />
          </div>

          <div class="brand-text">
            <div class="brand-title">AI Ops Agent</div>
            <div class="brand-sub">AI 运维自动修复系统</div>
          </div>
        </a>

        <nav class="nav-links" aria-label="主导航">
          <a href="#hero" :class="{ active: activeSection === 'hero' }">功能介绍</a>
          <a href="#solution" :class="{ active: activeSection === 'solution' }">核心能力</a>
          <a href="#architecture" :class="{ active: activeSection === 'architecture' }">处理流程</a>
          <a href="#demo" :class="{ active: activeSection === 'demo' }">动态演示</a>
          <a href="#pricing" :class="{ active: activeSection === 'pricing' }">报价方案</a>
          <a class="nav-link-primary" href="#contact">立即咨询</a>
        </nav>
      </div>
    </header>

    <!-- HERO -->
    <section id="hero" class="hero">
      <div class="container hero-grid">
        <div class="hero-left">
          <div class="badge">适配 Prometheus / Alertmanager / Kubernetes</div>

          <h1>
            让常见生产告警
            <br />
            自动完成诊断、修复与验证
          </h1>

          <p class="hero-desc">
            面向已有 Kubernetes 与 Prometheus 体系的技术团队。接入现有 Alertmanager 告警后，
            自动识别故障模式、生成处置方案、执行修复动作，并回查服务是否恢复，完整过程可审计、可复盘。
          </p>

          <div class="hero-actions">
            <a class="btn btn-primary" href="#contact">发送告警场景</a>
            <a class="btn btn-secondary" href="#demo">先看演示</a>
          </div>

          <div class="hero-metrics">
            <div class="metric">
              <div class="metric-value">对接现有告警体系</div>
              <div class="metric-label">Prometheus / Alertmanager 无需重做监控，直接接入现有流程</div>
            </div>
            <div class="metric">
              <div class="metric-value">覆盖诊断到验证闭环</div>
              <div class="metric-label">不是只给建议，而是覆盖诊断、执行、回查与恢复确认</div>
            </div>
            <div class="metric">
              <div class="metric-value">全流程可审计</div>
              <div class="metric-label">每一步动作、执行结果与恢复状态都可记录、复盘与追踪</div>
            </div>
          </div>
        </div>

        <div class="hero-right">
          <div class="ops-flow-card">
            <div class="ops-flow-head">
              <div>
                <div class="ops-flow-kicker">Live workflow</div>
                <h3>告警进入后，系统按闭环自动推进</h3>
              </div>
              <span class="ops-flow-badge">Processing</span>
            </div>

            <div class="ops-flow-list">
              <div class="ops-flow-item done">
                <div class="ops-flow-dot"></div>
                <div class="ops-flow-body">
                  <div class="ops-flow-title">Alert received</div>
                  <div class="ops-flow-desc">
                    收到 Alertmanager 告警，自动提取服务、环境、标签与最近异常信号。
                  </div>
                </div>
                <div class="ops-flow-state">已接入</div>
              </div>

              <div class="ops-flow-item done">
                <div class="ops-flow-dot"></div>
                <div class="ops-flow-body">
                  <div class="ops-flow-title">Root cause identified</div>
                  <div class="ops-flow-desc">
                    结合监控指标、历史处置记录与运行上下文，定位疑似根因并生成处置建议。
                  </div>
                </div>
                <div class="ops-flow-state">已诊断</div>
              </div>

              <div class="ops-flow-item active">
                <div class="ops-flow-dot"></div>
                <div class="ops-flow-body">
                  <div class="ops-flow-title">Runbook executed</div>
                  <div class="ops-flow-desc">
                    按预设 Runbook 执行修复动作，例如重启异常 Pod、回滚变更或恢复依赖连接。
                  </div>
                </div>
                <div class="ops-flow-state">执行中</div>
              </div>

              <div class="ops-flow-item pending">
                <div class="ops-flow-dot"></div>
                <div class="ops-flow-body">
                  <div class="ops-flow-title">Recovery verified</div>
                  <div class="ops-flow-desc">
                    自动回查错误率、延迟、实例状态与告警恢复情况，确认是否真正恢复。
                  </div>
                </div>
                <div class="ops-flow-state">待验证</div>
              </div>
            </div>

            <div class="ops-flow-foot">
              <div class="ops-flow-log-title">Recent action</div>
              <div class="ops-flow-log">
                已执行：restart deployment / checkout-service，当前正在等待指标回稳与告警恢复。
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- PROBLEM -->
    <section class="section soft-section">
      <div class="container narrow">
        <div class="section-title-wrap">
          <div class="section-tag">为什么需要它</div>
          <h2>真正耗时间的，不是告警，而是后续人工处理</h2>
          <p>
            Prometheus 可以发现问题，但很多团队仍然需要人工登录机器、
            查日志、定位原因、重启服务、验证恢复。
            当这些动作每天重复发生时，值班成本和恢复时间都会持续上升。
          </p>
        </div>

        <div class="problem-grid">
          <div class="problem-card">
            <div class="problem-icon">1</div>
            <h3>告警来了还要人工判断</h3>
            <p>值班同学仍要手动判断到底是进程挂了、端口异常还是 Pod 崩了。</p>
          </div>
          <div class="problem-card">
            <div class="problem-icon">2</div>
            <h3>修复动作重复且分散</h3>
            <p>很多问题最后都落到相似步骤：检查、重启、验证、再确认。</p>
          </div>
          <div class="problem-card">
            <div class="problem-icon">3</div>
            <h3>恢复之后还需要复核</h3>
            <p>执行动作不等于业务恢复，仍需要确认服务是否真的健康。</p>
          </div>
        </div>
      </div>
    </section>

    <!-- FEATURES -->
    <section id="solution" class="section">
      <div class="container">
        <div class="section-title-wrap">
          <div class="section-tag">核心能力</div>
          <h2>从告警进入，到恢复验证，形成处理闭环</h2>
          <p>
            当前产品聚焦最有落地价值的能力：
            接入 Prometheus 告警后，自动诊断、自动修复、自动验证。
          </p>
        </div>

        <div class="feature-grid">
          <div v-for="item in features" :key="item.title" class="feature-card">
            <h3>{{ item.title }}</h3>
            <p>{{ item.desc }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- ARCHITECTURE -->
    <section id="architecture" class="section soft-section">
      <div class="container">
        <div class="section-title-wrap">
          <div class="section-tag">处理流程</div>
          <h2>告警来了之后，系统会自动完成这些动作</h2>
          <p>
            接入现有 Prometheus / Alertmanager 后，
            系统会自动分析异常、执行修复动作并验证恢复结果，
            帮助团队减少人工介入，缩短常见故障的处理时间。
          </p>
        </div>

        <div class="arch-grid">
          <div class="arch-card">
            <img class="arch-icon" src="/icons/alert.png" alt="接收告警" />
            <h3>接收告警</h3>
            <p>接收来自 Prometheus / Alertmanager 的异常告警信息。</p>
          </div>

          <div class="arch-card">
            <img class="arch-icon" src="/icons/analyze.png" alt="自动分析" />
            <h3>自动分析</h3>
            <p>识别服务、环境与异常线索，判断当前问题属于哪类故障。</p>
          </div>

          <div class="arch-card">
            <img class="arch-icon" src="/icons/route.png" alt="匹配处理方式" />
            <h3>匹配处理方式</h3>
            <p>根据告警内容选择对应的处理场景和可执行修复动作。</p>
          </div>

          <div class="arch-card">
            <img class="arch-icon" src="/icons/plan.png" alt="生成处理步骤" />
            <h3>生成处理步骤</h3>
            <p>自动整理检查、修复和验证步骤，形成完整处理方案。</p>
          </div>

          <div class="arch-card">
            <img class="arch-icon" src="/icons/execute.png" alt="执行修复" />
            <h3>执行修复</h3>
            <p>自动执行重启、重建、检查等动作，减少人工重复操作。</p>
          </div>

          <div class="arch-card">
            <img class="arch-icon" src="/icons/verify.png" alt="确认恢复" />
            <h3>确认恢复</h3>
            <p>自动验证服务状态与恢复结果，避免“执行了但业务没恢复”。</p>
          </div>
        </div>
      </div>
    </section>

    <!-- SCENARIOS -->
    <section class="section">
      <div class="container">
        <div class="section-title-wrap">
          <div class="section-tag">典型场景</div>
          <h2>常见自动修复场景</h2>
          <p>
            针对常见服务异常与基础设施问题，
            系统可以自动完成诊断、执行修复动作并验证恢复结果，
            减少人工排查和重复运维操作。
          </p>
        </div>

        <div class="scenario-grid">
          <div v-for="item in scenarios" :key="item" class="scenario-card">
            {{ item }}
          </div>
        </div>
      </div>
    </section>

    <!-- DEMO -->
    <section id="demo" class="section soft-section">
      <div class="container video-wrap">
        <div class="section-title-wrap">
          <div class="section-tag">动态演示</div>
          <h2>一次典型自动修复链路</h2>
          <p>
            通过一个常见服务异常场景，演示系统如何自动完成诊断、修复与恢复验证。
          </p>
        </div>

        <div class="demo-card">
          <div class="demo-grid">
            <div class="demo-left">
              <div class="demo-steps">
                <div
                  v-for="(step, index) in demoSteps"
                  :key="step"
                  class="demo-step"
                  :class="{
                    done: index < demoCurrentStep,
                    active: index === demoCurrentStep,
                    pending: index > demoCurrentStep || demoCurrentStep === -1,
                  }"
                >
                  <div class="demo-step-marker">
                    <span v-if="index < demoCurrentStep">✓</span>
                    <span v-else>{{ index + 1 }}</span>
                  </div>

                  <div class="demo-step-body">
                    <div class="demo-step-title">{{ step }}</div>
                    <div class="demo-step-line"></div>
                  </div>
                </div>
              </div>

              <div class="demo-actions">
                <button class="btn btn-primary demo-play-btn" @click="playDemo" :disabled="demoPlaying">
                  {{ demoPlaying ? '演示进行中...' : (isDemoFinished ? '重新播放演示' : '播放演示') }}
                </button>
                <div class="demo-action-tip">
                  点击后按步骤推进，演示一次完整的自动修复流程
                </div>
              </div>
            </div>

            <div class="demo-right">
              <div class="demo-status-card">
                <div class="demo-status-header">
                  <div>
                    <div class="demo-status-kicker">当前状态信息</div>
                    <h3>nginx 异常自动修复</h3>
                  </div>
                  <div
                    class="demo-badge"
                    :class="{
                      success: demoState.result === '告警已清除',
                      processing: demoState.result === '处理中',
                    }"
                  >
                    {{ demoState.result === '-' ? '待演示' : demoState.result }}
                  </div>
                </div>

                <div class="demo-status-list">
                  <div class="demo-status-row">
                    <span>告警名称</span>
                    <strong>{{ demoState.alert }}</strong>
                  </div>
                  <div class="demo-status-row">
                    <span>目标服务</span>
                    <strong>{{ demoState.service }}</strong>
                  </div>
                  <div class="demo-status-row">
                    <span>当前步骤</span>
                    <strong>{{ demoState.step }}</strong>
                  </div>
                  <div class="demo-status-row">
                    <span>诊断结果</span>
                    <strong>{{ demoState.diagnosis }}</strong>
                  </div>
                  <div class="demo-status-row">
                    <span>执行动作</span>
                    <strong>{{ demoState.action }}</strong>
                  </div>
                  <div class="demo-status-row">
                    <span>恢复状态</span>
                    <strong>{{ demoState.recovery }}</strong>
                  </div>
                  <div class="demo-status-row">
                    <span>最终结论</span>
                    <strong class="demo-final">{{ demoState.result }}</strong>
                  </div>
                </div>

                <div class="demo-log-box">
                  <div class="demo-log-title">处理过程</div>
                  <pre class="demo-log">{{ demoLogs.join('\n') }}</pre>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- PRICING -->
    <section id="pricing" class="section">
      <div class="container">
        <div class="section-title-wrap">
          <div class="section-tag">报价方案</div>
          <h2>先落地一个场景，再逐步扩展到更多服务</h2>
          <p>
            当前以部署服务、场景接入和后续支持为主，
            适合先解决一个高频问题，跑通后再扩展到更多告警与修复场景。
          </p>
        </div>

        <div class="pricing-grid">
          <div class="price-card">
            <div class="price-name">试点验证版</div>
            <div class="price-value">¥9,800</div>
            <ul>
              <li>接入 1 个常见告警场景（如 nginx）</li>
              <li>完成自动诊断 → 修复 → 验证流程</li>
              <li>1~2 周内完成部署与试运行</li>
              <li>适合先验证自动修复效果</li>
            </ul>
          </div>

          <div class="price-card featured">
            <div class="featured-tag">主推方案</div>
            <div class="price-name">团队常用场景接入</div>
            <div class="price-value">¥50,000</div>
            <ul>
              <li>接入多个常见告警自动处理场景</li>
              <li>支持 Kubernetes / Prometheus 环境</li>
              <li>完成告警 → 修复 → 验证完整闭环</li>
              <li>适合 5-20 人技术团队落地使用</li>
            </ul>
          </div>

          <div class="price-card">
            <div class="price-name">复杂环境定制</div>
            <div class="price-value">¥80,000+</div>
            <ul>
              <li>按现有系统与架构深度集成</li>
              <li>支持复杂环境与多服务场景</li>
              <li>定制自动修复策略与执行流程</li>
              <li>按环境复杂度与需求范围报价</li>
            </ul>
          </div>
        </div>

        <div class="pricing-foot">
          后续支持：¥3000 - ¥8000 / 月，可按场景数量与支持范围确定
        </div>
      </div>
    </section>

    <!-- PAYMENT -->
    <section class="section soft-section">
      <div class="container narrow">
        <div class="section-title-wrap">
          <div class="section-tag">支付方式</div>
          <h2>支持个人付款与企业付款</h2>
          <p>
            当前支持微信、支付宝、对公转账，
            也支持合同签署与企业付款流程。
          </p>
        </div>

        <div class="payment-grid">
          <div class="payment-card payment-card-qrcode">
            <h3>微信收款</h3>
            <div class="payment-qrcode-wrap">
              <img class="payment-qrcode" src="/payment/wechat-pay.png" alt="微信收款二维码" />
            </div>
            <p>支持微信扫码付款</p>
          </div>

          <div class="payment-card payment-card-qrcode">
            <h3>支付宝</h3>
            <div class="payment-qrcode-wrap">
              <img class="payment-qrcode" src="/payment/alipay-pay.png" alt="支付宝收款二维码" />
            </div>
            <p>支持支付宝扫码付款</p>
          </div>

          <div class="payment-card payment-card-flow">
            <h3>企业合作</h3>

            <div class="payment-flow-panel">
              <div class="payment-flow-step">
                <span class="payment-flow-index">1</span>
                <span>确认需求与报价</span>
              </div>
              <div class="payment-flow-step">
                <span class="payment-flow-index">2</span>
                <span>签署合作协议</span>
              </div>
              <div class="payment-flow-step">
                <span class="payment-flow-index">3</span>
                <span>支持企业付款流程</span>
              </div>
              <div class="payment-flow-step">
                <span class="payment-flow-index">4</span>
                <span>按约定完成交付</span>
              </div>
            </div>

            <p>适合需要正式采购、合同流程与企业付款支持的客户</p>
          </div>
        </div>

      </div>
    </section>

    <!-- FAQ -->
    <section class="section">
      <div class="container narrow">
        <div class="section-title-wrap">
          <div class="section-tag">FAQ</div>
          <h2>客户通常会先问这些问题</h2>
          <p>关于接入方式、部署形式、付款流程，客户通常会先关心这些问题。</p>
        </div>

        <div class="faq-list">
          <div v-for="item in faqs" :key="item.q" class="faq-item">
            <h3>{{ item.q }}</h3>
            <p>{{ item.a }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- CONTACT -->
    <section id="contact" class="section contact-section">
      <div class="container narrow">
        <div class="section-title-wrap light">
          <div class="section-tag section-tag-light">联系咨询</div>
          <h2>欢迎先沟通一个真实告警场景</h2>
          <p>
            无论你是想先验证 1 个自动修复场景，
            还是希望评估 Kubernetes / Prometheus 环境下的接入方式，
            都可以通过微信或邮箱联系，快速沟通当前问题与落地路径。
          </p>
        </div>

        <div class="contact-grid">
          <div class="contact-panel">
            <div class="contact-panel-head">
              <span class="contact-label">微信</span>
              <h3>扫码快速沟通</h3>
            </div>

            <div class="contact-qrcode-wrap">
              <img
                class="contact-qrcode"
                src="/qrcode/wechat.png"
                alt="微信二维码"
              />
            </div>

            <p class="contact-desc">
              适合快速沟通具体告警场景、当前问题和接入方式
            </p>

            <div class="contact-tip">
              👉 添加后请备注：<strong>自动修复</strong>（我会优先通过）
            </div>
          </div>

          <div class="contact-panel">
            <div class="contact-panel-head">
              <span class="contact-label">邮箱</span>
              <h3>发送需求说明</h3>
            </div>

            <div class="contact-email-box">
              <a class="contact-email-link" href="mailto:jack_hq@126.com">
                jack_hq@126.com
              </a>
            </div>

            <p class="contact-desc">
              适合发送需求说明、合作咨询、企业采购信息和正式沟通材料。
            </p>

            <div class="contact-tip">
              👉 邮件中建议附上：告警名称、影响现象、当前环境、是否希望自动修复
            </div>
          </div>
        </div>

        <div class="contact-cta">
          <div class="contact-cta-title">建议直接发送一个真实告警示例</div>
          <p>
            例如：Prometheus Alert、日志片段、服务异常描述。
            我可以先帮你判断是否适合自动修复，以及大致落地方式
          </p>
        </div>
      </div>
    </section>

    <footer class="footer">
      <div class="container footer-inner">
        <div>© 2026 AI Ops Agent</div>
        <div>Prometheus 告警自动诊断与修复</div>
      </div>
    </footer>
  </main>
</template>

<style>
:root {
  --bg: #0b1020;
  --bg-soft: #f5f7fb;
  --card: #ffffff;
  --text: #121826;
  --muted: #5b6475;
  --line: #e6eaf2;
  --primary: #2563eb;
  --primary-dark: #1d4ed8;
  --shadow: 0 10px 30px rgba(15, 23, 42, 0.08);
  --radius: 20px;

  --space-section-y: 72px;
  --space-section-soft-y: 60px;
  --space-title-bottom: 34px;
  --space-grid: 20px;
  --space-card: 28px;
}

* {
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  margin: 0;
  color: var(--text);
  font-family: Inter, "PingFang SC", "Microsoft YaHei", Arial, sans-serif;
  background: #fff;
}

a {
  color: inherit;
  text-decoration: none;
}

button {
  font: inherit;
}

.page {
  min-height: 100vh;
}

.container {
  width: min(1200px, calc(100% - 40px));
  margin: 0 auto;
}

.narrow {
  width: min(920px, calc(100% - 40px));
}

/* nav */
.nav {
  position: sticky;
  top: 0;
  z-index: 40;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(14px);
  border-bottom: 1px solid rgba(230, 234, 242, 0.84);
}

.nav-inner {
  min-height: 80px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 32px;
}

.brand {
  display: flex;
  align-items: center;
  gap: 14px;
  text-decoration: none;
  flex-shrink: 0;
}

.brand-logo {
  width: 52px;
  height: 52px;
  padding: 6px;
  border-radius: 14px;
  background: linear-gradient(135deg, #3158f5, #aef3d2);
  box-shadow: 0 10px 24px rgba(49, 88, 245, 0.16);
  display: flex;
  align-items: center;
  justify-content: center;
}

.brand-logo img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  display: block;
}

.brand-text {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.brand-title {
  font-weight: 800;
  font-size: 17px;
  line-height: 1.15;
  color: var(--text);
  letter-spacing: -0.02em;
}

.brand-sub {
  margin-top: 4px;
  font-size: 12px;
  line-height: 1.3;
  color: var(--muted);
}

.nav-links {
  display: flex;
  align-items: center;
  gap: 8px;
  flex-wrap: wrap;
  justify-content: flex-end;
}

.nav-links a {
  position: relative;
  padding: 10px 12px;
  border-radius: 10px;
  color: var(--muted);
  font-size: 14px;
  font-weight: 700;
  transition: all 0.2s ease;
  white-space: nowrap;
}

.nav-links a:not(.nav-link-primary):hover {
  color: var(--text);
  background: rgb(174, 243, 210);
}

.nav-links a:not(.nav-link-primary).active {
  color: var(--primary);
  background: rgba(37, 99, 235, 0.08);
}

.nav-links a.active::after {
  content: '';
  position: absolute;
  bottom: 4px;
  left: 50%;
  transform: translateX(-50%);
  width: 16px;
  height: 3px;
  border-radius: 999px;
  background: var(--primary);
}

.nav-link-primary {
  color: #fff !important;
  background: linear-gradient(135deg, var(--primary), #4f46e5);
  box-shadow: 0 10px 22px rgba(37, 99, 235, 0.18);
  padding: 10px 16px !important;
}

.nav-link-primary:hover {
  transform: translateY(-1px);
  background: linear-gradient(135deg, #1d4ed8, #4338ca);
  color: #fff !important;
}

.nav-link-primary::after {
  display: none !important;
}

/* hero */
.hero {
  padding: 104px 0 80px;
  background:
    radial-gradient(circle at top left, rgba(37, 99, 235, 0.10), transparent 28%),
    radial-gradient(circle at 90% 10%, rgba(124, 58, 237, 0.10), transparent 22%),
    linear-gradient(180deg, #f8fbff 0%, #ffffff 100%);
}

.hero-grid {
  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(380px, 520px);
  gap: 72px;
  align-items: center;
}

.hero-left {
  max-width: 620px;
  padding-top: 0;
}

.badge {
  display: inline-flex;
  align-items: center;
  padding: 10px 14px;
  background: rgba(37, 99, 235, 0.08);
  border: 1px solid rgba(37, 99, 235, 0.12);
  color: var(--primary-dark);
  border-radius: 999px;
  font-size: 13px;
  font-weight: 600;
}

.hero h1 {
  font-size: 56px;
  line-height: 1.08;
  margin: 24px 0 18px;
  letter-spacing: -0.045em;
  max-width: 620px;
}

.hero-desc {
  font-size: 17px;
  line-height: 1.9;
  color: var(--muted);
  max-width: 620px;
}

.hero-actions {
  display: flex;
  gap: 14px;
  margin-top: 32px;
  margin-bottom: 8px;
  flex-wrap: wrap;
}

.btn {
  display: inline-flex;
  justify-content: center;
  align-items: center;
  height: 50px;
  padding: 0 24px;
  border-radius: 14px;
  font-weight: 700;
  transition: 0.2s ease;
  border: none;
  cursor: pointer;
}

.btn-primary {
  color: #fff;
  background: linear-gradient(135deg, var(--primary), #98e8b4);
  box-shadow: 0 12px 30px rgba(37, 99, 235, 0.22);
}

.btn-primary:hover {
  transform: translateY(-1px);
  background: linear-gradient(135deg, #1d4ed8, #4338ca);
  color: #fff;
  box-shadow: 0 16px 36px rgba(37, 99, 235, 0.28);
}

.btn-primary:disabled {
  opacity: 0.72;
  cursor: not-allowed;
  transform: none;
}

.btn-secondary {
  border: 1px solid var(--line);
  background: #fff;
}

.hero-metrics {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  margin-top: 32px;
}

.metric {
  min-width: 0;
  min-height: 128px;
  padding: 20px 18px;
  background: rgba(255, 255, 255, 0.92);
  border: 1px solid rgba(230, 234, 242, 0.95);
  border-radius: 18px;
  box-shadow: 0 10px 24px rgba(15, 23, 42, 0.05);
}

.metric-value {
  font-size: 18px;
  font-weight: 800;
  margin-bottom: 8px;
}

.metric-label {
  font-size: 14px;
  color: var(--muted);
  line-height: 1.65;
}

.hero-right {
  display: flex;
  justify-content: center;
  align-items: center;
}

.ops-flow-card {
  width: 100%;
  max-width: 520px;
  padding: 24px;
  border-radius: 28px;
  background:
    radial-gradient(circle at top right, rgba(37, 99, 235, 0.08), transparent 24%),
    linear-gradient(180deg, #ffffff 0%, #f8fbff 100%);
  border: 1px solid rgba(230, 234, 242, 0.95);
  box-shadow:
    0 24px 60px rgba(15, 23, 42, 0.10),
    0 8px 20px rgba(15, 23, 42, 0.05);
}

.ops-flow-head {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 16px;
  margin-bottom: 22px;
}

.ops-flow-kicker {
  font-size: 13px;
  font-weight: 700;
  color: var(--primary-dark);
  margin-bottom: 8px;
}

.ops-flow-head h3 {
  margin: 0;
  font-size: 24px;
  line-height: 1.3;
  color: var(--text);
}

.ops-flow-badge {
  flex-shrink: 0;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 92px;
  height: 36px;
  padding: 0 12px;
  border-radius: 999px;
  font-size: 13px;
  font-weight: 800;
  background: rgba(37, 99, 235, 0.10);
  color: var(--primary-dark);
}

.ops-flow-list {
  display: grid;
  gap: 14px;
}

.ops-flow-item {
  display: grid;
  grid-template-columns: 14px minmax(0, 1fr) auto;
  gap: 14px;
  align-items: start;
  padding: 16px 18px;
  border-radius: 18px;
  border: 1px solid #eef2f7;
  background: #ffffff;
}

.ops-flow-dot {
  width: 10px;
  height: 10px;
  margin-top: 7px;
  border-radius: 999px;
  background: #cbd5e1;
  box-shadow: 0 0 0 6px rgba(203, 213, 225, 0.16);
}

.ops-flow-body {
  min-width: 0;
}

.ops-flow-title {
  font-size: 15px;
  font-weight: 800;
  color: var(--text);
  margin-bottom: 6px;
}

.ops-flow-desc {
  font-size: 14px;
  line-height: 1.75;
  color: var(--muted);
}

.ops-flow-state {
  font-size: 12px;
  font-weight: 800;
  border-radius: 999px;
  padding: 6px 10px;
  white-space: nowrap;
  background: #eef2f7;
  color: #64748b;
}

.ops-flow-item.done .ops-flow-dot {
  background: #16a34a;
  box-shadow: 0 0 0 6px rgba(22, 163, 74, 0.14);
}

.ops-flow-item.done .ops-flow-state {
  background: rgba(22, 163, 74, 0.10);
  color: #15803d;
}

.ops-flow-item.active {
  border-color: rgba(37, 99, 235, 0.18);
  box-shadow: 0 12px 30px rgba(37, 99, 235, 0.08);
}

.ops-flow-item.active .ops-flow-dot {
  background: var(--primary);
  box-shadow: 0 0 0 6px rgba(37, 99, 235, 0.14);
}

.ops-flow-item.active .ops-flow-state {
  background: rgba(37, 99, 235, 0.10);
  color: var(--primary-dark);
}

.ops-flow-item.pending .ops-flow-dot {
  background: #cbd5e1;
}

.ops-flow-item.pending .ops-flow-state {
  background: #f1f5f9;
  color: #64748b;
}

.ops-flow-foot {
  margin-top: 18px;
  padding: 16px 18px;
  border-radius: 18px;
  background: #0f172a;
  color: #d1fae5;
}

.ops-flow-log-title {
  font-size: 13px;
  font-weight: 700;
  color: #93c5fd;
  margin-bottom: 8px;
}

.ops-flow-log {
  font-size: 13px;
  line-height: 1.8;
  font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace;
}

/* sections */
.section {
  padding: var(--space-section-y) 0;
  scroll-margin-top: 80px;
}

.soft-section {
  background: var(--bg-soft);
  padding: var(--space-section-soft-y) 0;
}

.section-title-wrap {
  text-align: center;
  margin-bottom: var(--space-title-bottom);
}

.section-title-wrap h2 {
  font-size: 38px;
  line-height: 1.2;
  margin: 18px 0 14px;
}

.section-title-wrap p {
  max-width: 760px;
  margin: 0 auto;
  color: var(--muted);
  line-height: 1.85;
  font-size: 17px;
}

.section-tag {
  display: inline-block;
  padding: 10px 16px;
  border-radius: 999px;
  background: #eef4ff;
  color: var(--primary-dark);
  font-size: 13px;
  font-weight: 700;
  letter-spacing: 0.04em;
}

#solution .section-title-wrap,
#architecture .section-title-wrap {
  margin-bottom: 28px;
}

/* grids */
.problem-grid,
.feature-grid,
.arch-grid,
.pricing-grid,
.payment-grid {
  gap: var(--space-grid);
}

.problem-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.feature-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
}

.arch-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.scenario-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 18px;
}

.pricing-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  align-items: stretch;
}

.payment-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

/* cards */
.problem-card,
.feature-card,
.scenario-card,
.payment-card,
.faq-item {
  background: #fff;
  border: 1px solid var(--line);
  border-radius: var(--radius);
  box-shadow: var(--shadow);
}

.problem-card,
.feature-card {
  padding: var(--space-card);
}

.arch-card {
  background: #fff;
  border: 1px solid var(--line);
  border-radius: 20px;
  box-shadow: var(--shadow);
  padding: 28px 24px;
  text-align: center;
}

.price-card {
  position: relative;
  padding: 30px 28px;
  background: #fff;
  border: 1px solid var(--line);
  border-radius: 24px;
  box-shadow: var(--shadow);
}

.payment-card {
  padding: 26px;
  text-align: center;
}

.scenario-card {
  padding: 22px 24px;
  font-weight: 600;
}

.faq-item {
  padding: 24px 26px;
}

/* problem */
.problem-icon {
  width: 42px;
  height: 42px;
  border-radius: 12px;
  display: grid;
  place-items: center;
  background: #eef4ff;
  color: var(--primary-dark);
  font-weight: 800;
  margin-bottom: 18px;
}

.problem-card h3,
.feature-card h3,
.payment-card h3,
.faq-item h3 {
  margin: 0 0 12px;
  font-size: 20px;
}

.problem-card p,
.feature-card p,
.payment-card p,
.faq-item p,
.pay-note {
  margin: 0;
  color: var(--muted);
  line-height: 1.8;
}

/* architecture */
.arch-icon {
  width: 64px;
  height: 64px;
  object-fit: contain;
  margin-bottom: 16px;
}

.arch-card h3 {
  margin: 0 0 12px;
  font-size: 20px;
}

.arch-card p {
  margin: 0;
  color: var(--muted);
  line-height: 1.8;
}

/* demo */
.video-wrap {
  text-align: center;
}

.demo-card {
  max-width: 1040px;
  margin: 0 auto;
  border-radius: 28px;
  overflow: hidden;
  box-shadow: var(--shadow);
  border: 1px solid var(--line);
  background:
    radial-gradient(circle at top right, rgba(37, 99, 235, 0.06), transparent 22%),
    #fff;
}

.demo-grid {
  display: grid;
  grid-template-columns: 0.95fr 1.05fr;
  gap: 0;
}

.demo-left {
  padding: 36px 30px 34px;
  border-right: 1px solid var(--line);
  background: linear-gradient(180deg, #fbfdff 0%, #f7faff 100%);
}

.demo-right {
  padding: 36px 30px 34px;
}

.demo-steps {
  display: flex;
  flex-direction: column;
  gap: 0;
}

.demo-step {
  display: grid;
  grid-template-columns: 48px 1fr;
  align-items: start;
  text-align: left;
}

.demo-step:not(:last-child) {
  min-height: 74px;
}

.demo-step-marker {
  width: 36px;
  height: 36px;
  border-radius: 999px;
  display: grid;
  place-items: center;
  font-size: 14px;
  font-weight: 800;
  border: 1px solid var(--line);
  background: #fff;
  color: #7b8698;
  position: relative;
  z-index: 1;
  margin-top: 2px;
}

.demo-step-body {
  position: relative;
  padding-top: 6px;
  min-height: 74px;
}

.demo-step-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text);
}

.demo-step-line {
  position: absolute;
  left: -30px;
  top: 40px;
  width: 2px;
  height: calc(100% - 8px);
  background: #e8edf5;
}

.demo-step:last-child .demo-step-line {
  display: none;
}

.demo-step.done .demo-step-marker {
  color: #fff;
  background: #16a34a;
  border-color: #16a34a;
  box-shadow: 0 8px 18px rgba(22, 163, 74, 0.18);
}

.demo-step.active .demo-step-marker {
  color: #fff;
  background: var(--primary);
  border-color: var(--primary);
  box-shadow: 0 10px 22px rgba(37, 99, 235, 0.22);
}

.demo-step.active .demo-step-title {
  color: var(--primary-dark);
}

.demo-step.pending .demo-step-marker {
  background: #fff;
  color: #94a3b8;
}

.demo-actions {
  margin-top: 20px;
  text-align: left;
}

.demo-play-btn {
  min-width: 168px;
}

.demo-action-tip {
  margin-top: 12px;
  font-size: 14px;
  color: var(--muted);
  line-height: 1.7;
}

.demo-status-card {
  text-align: left;
}

.demo-status-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 16px;
  margin-bottom: 22px;
}

.demo-status-kicker {
  font-size: 13px;
  font-weight: 700;
  color: var(--primary-dark);
  margin-bottom: 8px;
}

.demo-status-header h3 {
  margin: 0;
  font-size: 24px;
  line-height: 1.25;
}

.demo-badge {
  flex-shrink: 0;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 88px;
  height: 36px;
  padding: 0 12px;
  border-radius: 999px;
  font-size: 13px;
  font-weight: 800;
  background: #eef2f7;
  color: #475569;
}

.demo-badge.processing {
  background: rgba(37, 99, 235, 0.10);
  color: var(--primary-dark);
}

.demo-badge.success {
  background: rgba(22, 163, 74, 0.12);
  color: #15803d;
}

.demo-status-list {
  display: grid;
  gap: 12px;
}

.demo-status-row {
  min-height: 54px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 18px;
  padding: 0 18px;
  border-radius: 16px;
  background: #f8fafc;
  border: 1px solid #eef2f7;
}

.demo-status-row span {
  font-size: 14px;
  color: var(--muted);
}

.demo-status-row strong {
  font-size: 15px;
  color: var(--text);
  text-align: right;
}

.demo-final {
  color: #15803d;
}

.demo-log-box {
  margin-top: 20px;
  padding: 18px 18px 16px;
  border-radius: 18px;
  background: #0f172a;
  color: #d1fae5;
  border: 1px solid rgba(15, 23, 42, 0.08);
}

.demo-log-title {
  font-size: 13px;
  font-weight: 700;
  color: #93c5fd;
  margin-bottom: 10px;
}

.demo-log {
  margin: 0;
  white-space: pre-wrap;
  line-height: 1.9;
  font-size: 13px;
  font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace;
}

/* pricing */
.featured {
  border: 2px solid rgba(37, 99, 235, 0.22);
  transform: translateY(-6px);
}

.featured-tag {
  position: absolute;
  top: 14px;
  right: 14px;
  background: #eaf1ff;
  color: var(--primary-dark);
  font-size: 12px;
  font-weight: 700;
  border-radius: 999px;
  padding: 6px 10px;
}

.price-name {
  font-size: 20px;
  font-weight: 700;
  margin-bottom: 14px;
}

.price-value {
  font-size: 42px;
  font-weight: 800;
  margin-bottom: 18px;
  letter-spacing: -0.03em;
}

.price-card ul {
  padding-left: 18px;
  margin: 0;
  color: var(--muted);
  line-height: 2;
}

.pricing-foot {
  text-align: center;
  color: var(--muted);
  margin-top: 22px;
  font-size: 16px;
}

/* payment / faq */
.pay-note {
  margin-top: 18px;
  text-align: center;
}

.faq-list {
  display: grid;
  gap: 16px;
}

/* contact */
.contact-section {
  background:
    radial-gradient(circle at top center, rgba(37, 99, 235, 0.14), transparent 30%),
    linear-gradient(135deg, #0f172a, #0b1220 58%, #1e293b);
  color: #fff;
}

.section-title-wrap.light p {
  color: rgba(255, 255, 255, 0.76);
}

.section-tag-light {
  background: rgba(255, 255, 255, 0.10);
  color: #dbeafe;
}

.contact-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 24px;
  max-width: 860px;
  margin: 0 auto;
}

.contact-panel {
  padding: 30px 28px;
  border-radius: 24px;
  background: linear-gradient(180deg, rgba(255, 255, 255, 0.06), rgba(255, 255, 255, 0.04));
  border: 1px solid rgba(255, 255, 255, 0.10);
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.18);
  text-align: center;
}

.contact-panel-head {
  margin-bottom: 18px;
}

.contact-label {
  display: inline-block;
  margin-bottom: 10px;
  padding: 6px 10px;
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.08);
  color: rgba(255, 255, 255, 0.78);
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.04em;
}

.contact-panel h3 {
  margin: 0;
  font-size: 24px;
  line-height: 1.25;
  color: #fff;
}

.contact-qrcode-wrap {
  display: flex;
  justify-content: center;
  margin: 8px 0 20px;
}

.contact-qrcode {
  width: 220px;
  height: 220px;
  padding: 12px;
  background: #fff;
  border-radius: 20px;
  object-fit: contain;
  box-shadow: 0 16px 40px rgba(15, 23, 42, 0.24);
}

.contact-email-box {
  margin: 14px 0 20px;
  padding: 22px 18px;
  border-radius: 18px;
  background: rgba(255, 255, 255, 0.06);
  border: 1px solid rgba(255, 255, 255, 0.08);
}

.contact-email-link {
  font-size: 32px;
  line-height: 1.2;
  font-weight: 800;
  color: #fff;
  text-decoration: none;
  word-break: break-all;
  letter-spacing: -0.02em;
}

.contact-email-link:hover {
  text-decoration: underline;
}

.contact-desc {
  margin: 0;
  color: rgba(255, 255, 255, 0.78);
  line-height: 1.9;
  font-size: 16px;
}

.contact-tip {
  margin-top: 18px;
  padding: 14px 16px;
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.07);
  border: 1px solid rgba(255, 255, 255, 0.08);
  color: #e2e8f0;
  line-height: 1.8;
  font-size: 15px;
  text-align: left;
}

.contact-cta {
  max-width: 860px;
  margin: 24px auto 0;
  padding: 22px 24px;
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.10);
  text-align: center;
}

.contact-cta-title {
  font-size: 20px;
  font-weight: 800;
  color: #fff;
  margin-bottom: 10px;
}

.contact-cta p {
  margin: 0;
  color: rgba(255, 255, 255, 0.82);
  line-height: 1.9;
  font-size: 16px;
}

/* footer */
.footer {
  background: #0b1220;
  color: rgba(255, 255, 255, 0.68);
  border-top: 1px solid rgba(255, 255, 255, 0.06);
}

.footer-inner {
  min-height: 72px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 14px;
}

.payment-card-qrcode {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.payment-qrcode-wrap {
  display: flex;
  justify-content: center;
  margin: 14px 0 16px;
}

.payment-qrcode {
  width: 180px;
  height: 180px;
  padding: 10px;
  background: #fff;
  border-radius: 18px;
  object-fit: contain;
  box-shadow: 0 12px 30px rgba(15, 23, 42, 0.10);
}

.payment-card-flow {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.payment-flow-panel {
  width: 100%;
  max-width: 220px;
  margin: 14px 0 16px;
  padding: 18px 16px;
  border-radius: 18px;
  background: linear-gradient(180deg, #f8fafc 0%, #ffffff 100%);
  border: 1px solid #eef2f7;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.8);
}

.payment-flow-step {
  display: flex;
  align-items: center;
  gap: 10px;
  text-align: left;
  font-size: 14px;
  color: var(--text);
  line-height: 1.5;
}

.payment-flow-step:not(:last-child) {
  margin-bottom: 12px;
}

.payment-flow-index {
  width: 24px;
  height: 24px;
  border-radius: 999px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  background: #eef4ff;
  color: var(--primary-dark);
  font-size: 12px;
  font-weight: 800;
}

.payment-card-flow p {
  max-width: 220px;
}

/* responsive */
@media (max-width: 1024px) {
  .hero-grid,
  .problem-grid,
  .feature-grid,
  .pricing-grid,
  .payment-grid,
  .arch-grid,
  .demo-grid {
    grid-template-columns: 1fr;
  }

  .hero {
    padding: 72px 0 64px;
  }

  .hero-left {
    max-width: 100%;
    padding-top: 0;
  }

  .hero-right {
    justify-content: flex-start;
  }

  .hero h1 {
    font-size: 48px;
    max-width: 100%;
  }

  .hero-metrics {
    grid-template-columns: 1fr;
  }

  .featured {
    transform: none;
  }

  .demo-left {
    border-right: none;
    border-bottom: 1px solid var(--line);
  }

  .footer-inner {
    flex-direction: column;
    justify-content: center;
    gap: 10px;
    padding: 14px 0;
  }
}

@media (max-width: 768px) {
  :root {
    --space-section-y: 60px;
    --space-section-soft-y: 52px;
    --space-title-bottom: 28px;
    --space-grid: 16px;
    --space-card: 24px;
  }

  .payment-qrcode {
    width: 160px;
    height: 160px;
  }

  .nav {
    padding-top: 8px;
    background: rgba(255, 255, 255, 0.97);
  }

  .nav-inner {
    min-height: auto;
    padding: 12px 0 14px;
    flex-direction: column;
    align-items: stretch;
    gap: 12px;
  }

  .brand {
    justify-content: center;
  }

  .brand-text {
    text-align: left;
  }

  .brand-title {
    font-size: 17px;
  }

  .brand-sub {
    font-size: 12px;
  }

  .nav-links {
    gap: 8px;
    justify-content: center;
  }

  .nav-links a {
    padding: 9px 12px;
    font-size: 13px;
  }

  .nav-links a.active::after {
    bottom: 2px;
  }

  .hero {
    padding: 58px 0 54px;
  }

  .hero h1 {
    font-size: 38px;
    line-height: 1.14;
  }

  .hero-desc {
    font-size: 16px;
    line-height: 1.8;
  }

  .section-title-wrap h2 {
    font-size: 30px;
  }

  .scenario-grid {
    grid-template-columns: 1fr;
  }

  .demo-left,
  .demo-right {
    padding: 24px 20px;
  }

  .demo-status-header {
    flex-direction: column;
    align-items: flex-start;
  }

  .demo-status-row {
    min-height: auto;
    padding: 14px 16px;
    flex-direction: column;
    align-items: flex-start;
  }

  .demo-status-row strong {
    text-align: left;
  }

  .ops-flow-card {
    padding: 20px;
  }

  .ops-flow-head {
    flex-direction: column;
    align-items: flex-start;
  }

  .ops-flow-head h3 {
    font-size: 20px;
  }

  .ops-flow-item {
    grid-template-columns: 14px minmax(0, 1fr);
  }

  .ops-flow-state {
    grid-column: 2;
    justify-self: start;
    margin-top: 8px;
  }

  .contact-grid {
    grid-template-columns: 1fr;
    gap: 18px;
  }

  .contact-panel {
    padding: 24px 20px;
  }

  .contact-panel h3 {
    font-size: 22px;
  }

  .contact-qrcode {
    width: 190px;
    height: 190px;
    padding: 10px;
  }

  .contact-email-link {
    font-size: 26px;
  }

  .contact-desc,
  .contact-tip,
  .contact-cta p {
    font-size: 15px;
  }

  .contact-cta {
    padding: 18px 18px;
  }

  .contact-cta-title {
    font-size: 18px;
  }
}
</style>