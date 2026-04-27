/* ============================================
   ForenSolve Experts - JavaScript
   ============================================ */

// === Particle Background ===
class ParticleSystem {
  constructor(canvas) {
    this.canvas = canvas;
    this.ctx = canvas.getContext('2d');
    this.particles = [];
    this.connections = [];
    this.resize();
    window.addEventListener('resize', () => this.resize());
    this.init();
    this.animate();
  }

  resize() {
    this.canvas.width = window.innerWidth;
    this.canvas.height = window.innerHeight;
  }

  init() {
    const count = Math.min(80, Math.floor(window.innerWidth / 20));
    for (let i = 0; i < count; i++) {
      this.particles.push({
        x: Math.random() * this.canvas.width,
        y: Math.random() * this.canvas.height,
        vx: (Math.random() - 0.5) * 0.5,
        vy: (Math.random() - 0.5) * 0.5,
        size: Math.random() * 2 + 0.5,
        opacity: Math.random() * 0.5 + 0.1
      });
    }
  }

  animate() {
    this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
    this.particles.forEach((p, i) => {
      p.x += p.vx;
      p.y += p.vy;
      if (p.x < 0 || p.x > this.canvas.width) p.vx *= -1;
      if (p.y < 0 || p.y > this.canvas.height) p.vy *= -1;
      this.ctx.beginPath();
      this.ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
      this.ctx.fillStyle = `rgba(0, 229, 255, ${p.opacity})`;
      this.ctx.fill();
      for (let j = i + 1; j < this.particles.length; j++) {
        const p2 = this.particles[j];
        const dist = Math.hypot(p.x - p2.x, p.y - p2.y);
        if (dist < 150) {
          this.ctx.beginPath();
          this.ctx.moveTo(p.x, p.y);
          this.ctx.lineTo(p2.x, p2.y);
          this.ctx.strokeStyle = `rgba(0, 229, 255, ${0.08 * (1 - dist / 150)})`;
          this.ctx.lineWidth = 0.5;
          this.ctx.stroke();
        }
      }
    });
    requestAnimationFrame(() => this.animate());
  }
}

// === Navbar Scroll Effect ===
function initNavbar() {
  const navbar = document.querySelector('.navbar');
  const toggle = document.querySelector('.nav-toggle');
  const links = document.querySelector('.nav-links');

  window.addEventListener('scroll', () => {
    navbar.classList.toggle('scrolled', window.scrollY > 50);
  });

  if (toggle) {
    toggle.addEventListener('click', () => {
      links.classList.toggle('active');
      toggle.innerHTML = links.classList.contains('active')
        ? '<i class="fas fa-times"></i>'
        : '<i class="fas fa-bars"></i>';
    });
  }

  document.querySelectorAll('.nav-links a').forEach(a => {
    a.addEventListener('click', () => {
      links.classList.remove('active');
      if (toggle) toggle.innerHTML = '<i class="fas fa-bars"></i>';
    });
  });
}

// === Counter Animation ===
function animateCounters() {
  const counters = document.querySelectorAll('[data-count]');
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting && !entry.target.dataset.animated) {
        entry.target.dataset.animated = 'true';
        const target = parseInt(entry.target.dataset.count);
        const suffix = entry.target.dataset.suffix || '';
        const prefix = entry.target.dataset.prefix || '';
        const duration = 2000;
        const start = performance.now();
        function update(now) {
          const elapsed = now - start;
          const progress = Math.min(elapsed / duration, 1);
          const eased = 1 - Math.pow(1 - progress, 3);
          const current = Math.floor(target * eased);
          entry.target.textContent = prefix + current.toLocaleString() + suffix;
          if (progress < 1) requestAnimationFrame(update);
        }
        requestAnimationFrame(update);
      }
    });
  }, { threshold: 0.5 });

  counters.forEach(c => observer.observe(c));
}

// === Scroll Reveal ===
function initReveal() {
  const elements = document.querySelectorAll('.reveal');
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('active');
      }
    });
  }, { threshold: 0.1, rootMargin: '0px 0px -50px 0px' });

  elements.forEach(el => observer.observe(el));
}

// === Back to Top ===
function initBackToTop() {
  const btn = document.querySelector('.back-to-top');
  if (!btn) return;
  window.addEventListener('scroll', () => {
    btn.classList.toggle('visible', window.scrollY > 500);
  });
  btn.addEventListener('click', () => {
    window.scrollTo({ top: 0, behavior: 'smooth' });
  });
}

// === Charts (Chart.js) ===
function initCharts() {
  if (typeof Chart === 'undefined') return;

  Chart.defaults.color = '#90a4ae';
  Chart.defaults.borderColor = 'rgba(0, 229, 255, 0.08)';
  Chart.defaults.font.family = "'Inter', sans-serif";

  // Crime Trend Line Chart
  const trendCtx = document.getElementById('crimeTrendChart');
  if (trendCtx) {
    new Chart(trendCtx, {
      type: 'line',
      data: {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
        datasets: [
          {
            label: 'Homicide',
            data: [12, 15, 11, 18, 14, 20, 22, 19, 16, 13, 17, 21],
            borderColor: '#ff1744',
            backgroundColor: 'rgba(255, 23, 68, 0.1)',
            tension: 0.4,
            fill: true,
            pointRadius: 3,
            pointBackgroundColor: '#ff1744',
            borderWidth: 2
          },
          {
            label: 'Cybercrime',
            data: [28, 35, 42, 38, 45, 52, 48, 55, 62, 58, 65, 72],
            borderColor: '#00e5ff',
            backgroundColor: 'rgba(0, 229, 255, 0.1)',
            tension: 0.4,
            fill: true,
            pointRadius: 3,
            pointBackgroundColor: '#00e5ff',
            borderWidth: 2
          },
          {
            label: 'Fraud',
            data: [22, 25, 30, 28, 32, 35, 38, 33, 36, 40, 42, 45],
            borderColor: '#ffd600',
            backgroundColor: 'rgba(255, 214, 0, 0.1)',
            tension: 0.4,
            fill: true,
            pointRadius: 3,
            pointBackgroundColor: '#ffd600',
            borderWidth: 2
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { position: 'top', labels: { usePointStyle: true, padding: 20 } },
          tooltip: {
            backgroundColor: 'rgba(17, 24, 39, 0.95)',
            borderColor: 'rgba(0, 229, 255, 0.2)',
            borderWidth: 1,
            padding: 12,
            cornerRadius: 8
          }
        },
        scales: {
          y: { beginAtZero: true, grid: { color: 'rgba(0, 229, 255, 0.05)' } },
          x: { grid: { display: false } }
        }
      }
    });
  }

  // Case Distribution Doughnut
  const distCtx = document.getElementById('caseDistChart');
  if (distCtx) {
    new Chart(distCtx, {
      type: 'doughnut',
      data: {
        labels: ['Homicide', 'Cybercrime', 'Fraud', 'Narcotics', 'Arson', 'Other'],
        datasets: [{
          data: [18, 28, 22, 15, 8, 9],
          backgroundColor: [
            '#ff1744', '#00e5ff', '#ffd600', '#00e676', '#ff6e40', '#ce93d8'
          ],
          borderColor: '#111827',
          borderWidth: 3,
          hoverOffset: 8
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        cutout: '65%',
        plugins: {
          legend: { position: 'bottom', labels: { usePointStyle: true, padding: 16 } },
          tooltip: {
            backgroundColor: 'rgba(17, 24, 39, 0.95)',
            borderColor: 'rgba(0, 229, 255, 0.2)',
            borderWidth: 1,
            padding: 12,
            cornerRadius: 8
          }
        }
      }
    });
  }

  // Resolution Rate Bar Chart
  const resCtx = document.getElementById('resolutionChart');
  if (resCtx) {
    new Chart(resCtx, {
      type: 'bar',
      data: {
        labels: ['2019', '2020', '2021', '2022', '2023', '2024', '2025'],
        datasets: [
          {
            label: 'Cases Filed',
            data: [320, 380, 410, 455, 520, 580, 640],
            backgroundColor: 'rgba(0, 229, 255, 0.6)',
            borderColor: '#00e5ff',
            borderWidth: 1,
            borderRadius: 6,
            barPercentage: 0.6
          },
          {
            label: 'Cases Resolved',
            data: [280, 340, 375, 420, 490, 545, 610],
            backgroundColor: 'rgba(0, 230, 118, 0.6)',
            borderColor: '#00e676',
            borderWidth: 1,
            borderRadius: 6,
            barPercentage: 0.6
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { position: 'top', labels: { usePointStyle: true, padding: 20 } },
          tooltip: {
            backgroundColor: 'rgba(17, 24, 39, 0.95)',
            borderColor: 'rgba(0, 229, 255, 0.2)',
            borderWidth: 1,
            padding: 12,
            cornerRadius: 8
          }
        },
        scales: {
          y: { beginAtZero: true, grid: { color: 'rgba(0, 229, 255, 0.05)' } },
          x: { grid: { display: false } }
        }
      }
    });
  }

  // Modus Operandi Radar Chart
  const moCtx = document.getElementById('moRadarChart');
  if (moCtx) {
    new Chart(moCtx, {
      type: 'radar',
      data: {
        labels: ['Premeditated', 'Opportunistic', 'Organized', 'Serial', 'Tech-Enabled', 'Insider'],
        datasets: [
          {
            label: '2024',
            data: [78, 62, 85, 45, 92, 58],
            borderColor: '#00e5ff',
            backgroundColor: 'rgba(0, 229, 255, 0.15)',
            pointBackgroundColor: '#00e5ff',
            borderWidth: 2,
            pointRadius: 4
          },
          {
            label: '2025',
            data: [72, 68, 80, 52, 95, 65],
            borderColor: '#ff6e40',
            backgroundColor: 'rgba(255, 110, 64, 0.15)',
            pointBackgroundColor: '#ff6e40',
            borderWidth: 2,
            pointRadius: 4
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: { position: 'top', labels: { usePointStyle: true, padding: 20 } }
        },
        scales: {
          r: {
            beginAtZero: true,
            max: 100,
            ticks: { display: false },
            grid: { color: 'rgba(0, 229, 255, 0.08)' },
            angleLines: { color: 'rgba(0, 229, 255, 0.08)' },
            pointLabels: { color: '#90a4ae', font: { size: 11 } }
          }
        }
      }
    });
  }
}

// === Heatmap Generator ===
function initHeatmap() {
  const grid = document.getElementById('crimeHeatmap');
  if (!grid) return;

  const hours = ['12a', '3a', '6a', '9a', '12p', '3p', '6p', '9p'];
  const days = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
  const data = [
    [2, 1, 1, 3, 5, 8, 12, 9],
    [3, 1, 2, 4, 6, 9, 11, 8],
    [2, 1, 1, 3, 7, 10, 14, 10],
    [4, 2, 1, 5, 8, 11, 13, 9],
    [5, 3, 2, 6, 9, 14, 18, 15],
    [8, 5, 3, 4, 7, 12, 20, 18],
    [6, 4, 2, 3, 5, 10, 16, 14]
  ];

  // Add hour labels
  grid.innerHTML = '<div></div>';
  hours.forEach(h => {
    grid.innerHTML += `<div class="heatmap-label">${h}</div>`;
  });

  days.forEach((day, di) => {
    grid.innerHTML += `<div class="heatmap-label" style="display:flex;align-items:center;justify-content:flex-end;padding-right:8px;">${day}</div>`;
    data[di].forEach(val => {
      const max = 20;
      const intensity = val / max;
      let color;
      if (intensity < 0.3) color = `rgba(0, 229, 255, ${0.15 + intensity * 0.3})`;
      else if (intensity < 0.6) color = `rgba(255, 214, 0, ${0.3 + intensity * 0.4})`;
      else color = `rgba(255, 23, 68, ${0.4 + intensity * 0.5})`;
      grid.innerHTML += `<div class="heatmap-cell" style="background:${color};" title="${day} ${hours[data[di].indexOf(val)]}: ${val} incidents"></div>`;
    });
  });

  // Fix grid columns for label + 8 data columns
  grid.style.gridTemplateColumns = '48px repeat(8, 1fr)';
}

// === Correlation Matrix ===
function initCorrelationMatrix() {
  const matrix = document.getElementById('correlationMatrix');
  if (!matrix) return;

  const types = ['Hom.', 'Cyber', 'Fraud', 'Narc.', 'Arson'];
  const correlations = [
    [1.00, 0.12, 0.25, 0.65, 0.35],
    [0.12, 1.00, 0.78, 0.08, 0.05],
    [0.25, 0.78, 1.00, 0.15, 0.10],
    [0.65, 0.08, 0.15, 1.00, 0.42],
    [0.35, 0.05, 0.10, 0.42, 1.00]
  ];

  // Header row
  matrix.innerHTML = '<div></div>';
  types.forEach(t => {
    matrix.innerHTML += `<div class="matrix-header">${t}</div>`;
  });

  types.forEach((t, i) => {
    matrix.innerHTML += `<div class="matrix-header" style="writing-mode:horizontal-tb;">${t}</div>`;
    correlations[i].forEach((val, j) => {
      const r = Math.floor(val * 255);
      const g = Math.floor((1 - val) * 100);
      const b = Math.floor((1 - val) * 200);
      let bg;
      if (i === j) bg = 'rgba(0, 229, 255, 0.3)';
      else if (val > 0.6) bg = `rgba(255, 23, 68, ${val * 0.5})`;
      else if (val > 0.3) bg = `rgba(255, 214, 0, ${val * 0.6})`;
      else bg = `rgba(0, 229, 255, ${val * 0.4 + 0.1})`;
      matrix.innerHTML += `<div class="matrix-cell" style="background:${bg};" title="${types[i]} ↔ ${types[j]}: ${val.toFixed(2)}">${val.toFixed(2)}</div>`;
    });
  });

  matrix.style.gridTemplateColumns = `60px repeat(${types.length}, 1fr)`;
}

// === Hotspot Bars Animation ===
function initHotspots() {
  const bars = document.querySelectorAll('.hotspot-bar');
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const width = entry.target.dataset.width;
        entry.target.style.width = width + '%';
      }
    });
  }, { threshold: 0.5 });

  bars.forEach(bar => {
    bar.style.width = '0%';
    observer.observe(bar);
  });
}

// === Live Clock ===
function initClock() {
  const clockEl = document.getElementById('liveClock');
  if (!clockEl) return;
  function update() {
    const now = new Date();
    clockEl.textContent = now.toLocaleTimeString('en-US', {
      hour12: false, hour: '2-digit', minute: '2-digit', second: '2-digit'
    }) + ' IST';
  }
  update();
  setInterval(update, 1000);
}

// === Smooth Scroll for Nav Links ===
function initSmoothScroll() {
  document.querySelectorAll('a[href^="#"]').forEach(anchor => {
    anchor.addEventListener('click', function (e) {
      e.preventDefault();
      const target = document.querySelector(this.getAttribute('href'));
      if (target) {
        const offset = 80;
        const pos = target.getBoundingClientRect().top + window.scrollY - offset;
        window.scrollTo({ top: pos, behavior: 'smooth' });
      }
    });
  });
}

// === Contact Form ===
function initContactForm() {
  const form = document.getElementById('contactForm');
  if (!form) return;
  form.addEventListener('submit', (e) => {
    e.preventDefault();
    const btn = form.querySelector('.btn-primary');
    const origText = btn.innerHTML;
    btn.innerHTML = '<i class="fas fa-check"></i> Message Sent!';
    btn.style.background = 'linear-gradient(135deg, #00e676, #00c853)';
    setTimeout(() => {
      btn.innerHTML = origText;
      btn.style.background = '';
      form.reset();
    }, 3000);
  });
}

// === Typing Effect ===
function initTypingEffect() {
  const el = document.getElementById('typingText');
  if (!el) return;
  const phrases = [
    'Crime Pattern Analysis',
    'Digital Forensics',
    'Evidence Correlation',
    'Criminal Profiling',
    'Forensic Analytics'
  ];
  let phraseIdx = 0, charIdx = 0, isDeleting = false;

  function type() {
    const current = phrases[phraseIdx];
    if (isDeleting) {
      el.textContent = current.substring(0, charIdx--);
      if (charIdx < 0) {
        isDeleting = false;
        phraseIdx = (phraseIdx + 1) % phrases.length;
        setTimeout(type, 500);
        return;
      }
    } else {
      el.textContent = current.substring(0, ++charIdx);
      if (charIdx === current.length) {
        isDeleting = true;
        setTimeout(type, 2000);
        return;
      }
    }
    setTimeout(type, isDeleting ? 40 : 80);
  }
  type();
}

// === Initialize Everything ===
document.addEventListener('DOMContentLoaded', () => {
  // Particles
  const canvas = document.getElementById('particles-canvas');
  if (canvas) new ParticleSystem(canvas);

  initNavbar();
  initSmoothScroll();
  initReveal();
  animateCounters();
  initBackToTop();
  initCharts();
  initHeatmap();
  initCorrelationMatrix();
  initHotspots();
  initClock();
  initContactForm();
  initTypingEffect();
});