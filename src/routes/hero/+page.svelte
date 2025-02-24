<script>
    import { onMount } from 'svelte';
    
    let countdown = 'Loading...';
    let h1Text = '🚀 TechNext 2025';
    let subtitle = 'Innovate. Connect. Lead.';
    let displayedText = '';
    let displayedSubtitle = '';
    let index = 0;
    let subIndex = 0;
  
    function typeWriter() {
      if (index < h1Text.length) {
        displayedText += h1Text.charAt(index);
        index++;
        setTimeout(typeWriter, 100);
      } else if (subIndex < subtitle.length) {
        displayedSubtitle += subtitle.charAt(subIndex);
        subIndex++;
        setTimeout(typeWriter, 50);
      }
    }
  
    function updateCountdown() {
      const eventDate = new Date('2025-03-15T09:00:00').getTime();
      setInterval(() => {
        const now = new Date().getTime();
        const difference = eventDate - now;
        const days = Math.floor(difference / (1000 * 60 * 60 * 24));
        const hours = Math.floor((difference % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
        const minutes = Math.floor((difference % (1000 * 60 * 60)) / (1000 * 60));
        countdown = `${days}d : ${hours}h : ${minutes}m`;
      }, 1000);
    }
  
    onMount(() => {
      typeWriter();
      updateCountdown();
    });
  </script>
  
  <div class="hero">
    <div class="gradient-bg"></div>
    <div class="content">
      <div class="text-content">
        <h1>{displayedText}</h1>
        <h2>{displayedSubtitle}</h2>
        <p class="description">
          Join the ultimate gathering of visionaries, developers, and industry leaders 
          shaping the future of technology.
        </p>
        <div class="event-details">
          <div class="detail">
            <span class="icon">📍</span>
            <span>San Francisco, CA</span>
          </div>
          <div class="detail">
            <span class="icon">📅</span>
            <span>March 15-17, 2025</span>
          </div>
        </div>
        <div class="countdown-container">
          <div class="countdown-box">
            <span class="countdown-text">Time Remaining</span>
            <span class="countdown-timer">{countdown}</span>
          </div>
        </div>
        <div class="cta-buttons">
          <button class="primary-btn">Register Now</button>
          <button class="secondary-btn">Learn More</button>
        </div>
      </div>
      <div class="image-content">
        <div class="speaker-card">
          <img src="speaker1.png" alt="Speaker" />
          <div class="speaker-info">
            <h3>Stefen Prakash</h3>
            <p>AI Scientist & Keynote Speaker</p>
          </div>
        </div>
      </div>
    </div>
  </div>
  
  <style>
    .hero {
      min-height: 100vh;
      position: relative;
      overflow: hidden;
      background: #0a0a0a;
      color: white;
    }
  
    .gradient-bg {
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: linear-gradient(45deg, #000428, #004e92);
      opacity: 0.8;
    }
  
    .content {
      position: relative;
      max-width: 1400px;
      margin: 0 auto;
      padding: 4rem 2rem;
      display: grid;
      grid-template-columns: 1.2fr 0.8fr;
      gap: 4rem;
      align-items: center;
    }
  
    .text-content h1 {
      font-size: 4rem;
      font-weight: 800;
      margin: 0;
      background: linear-gradient(120deg, #00ff87 0%, #60efff 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
  
    .text-content h2 {
      font-size: 2rem;
      color: #60efff;
      margin: 1rem 0;
      font-weight: 400;
    }
  
    .description {
      font-size: 1.2rem;
      color: #a0aec0;
      margin: 2rem 0;
      line-height: 1.6;
    }
  
    .event-details {
      display: flex;
      gap: 2rem;
      margin: 2rem 0;
    }
  
    .detail {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      color: #60efff;
    }
  
    .countdown-container {
      margin: 2rem 0;
    }
  
    .countdown-box {
      background: rgba(255, 255, 255, 0.1);
      padding: 1rem 2rem;
      border-radius: 12px;
      backdrop-filter: blur(10px);
      display: inline-flex;
      flex-direction: column;
      gap: 0.5rem;
    }
  
    .countdown-text {
      font-size: 0.9rem;
      color: #60efff;
    }
  
    .countdown-timer {
      font-size: 2rem;
      font-weight: 700;
      color: white;
    }
  
    .cta-buttons {
      display: flex;
      gap: 1rem;
      margin-top: 2rem;
    }
  
    .primary-btn, .secondary-btn {
      padding: 1rem 2rem;
      border-radius: 8px;
      font-weight: 600;
      font-size: 1rem;
      transition: all 0.3s ease;
      cursor: pointer;
    }
  
    .primary-btn {
      background: linear-gradient(120deg, #00ff87 0%, #60efff 100%);
      border: none;
      color: #000428;
    }
  
    .secondary-btn {
      background: transparent;
      border: 2px solid #60efff;
      color: #60efff;
    }
  
    .speaker-card {
      background: rgba(255, 255, 255, 0.1);
      border-radius: 20px;
      overflow: hidden;
      backdrop-filter: blur(10px);
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
      transition: transform 0.3s ease;
    }
  
    .speaker-card:hover {
      transform: translateY(-10px);
    }
  
    .speaker-card img {
      width: 100%;
      height: auto;
      object-fit: cover;
    }
  
    .speaker-info {
      padding: 1.5rem;
      text-align: center;
    }
  
    .speaker-info h3 {
      margin: 0;
      font-size: 1.5rem;
      color: white;
    }
  
    .speaker-info p {
      margin: 0.5rem 0 0;
      color: #60efff;
    }
  
    @media (max-width: 1024px) {
      .content {
        grid-template-columns: 1fr;
        gap: 2rem;
      }
  
      .text-content h1 {
        font-size: 3rem;
      }
  
      .text-content h2 {
        font-size: 1.5rem;
      }
  
      .speaker-card {
        max-width: 400px;
        margin: 0 auto;
      }
    }
  
    @media (max-width: 768px) {
      .content {
        padding: 2rem 1rem;
      }
  
      .text-content h1 {
        font-size: 2.5rem;
      }
  
      .event-details {
        flex-direction: column;
        gap: 1rem;
      }
  
      .cta-buttons {
        flex-direction: column;
      }
  
      .countdown-box {
        width: 100%;
        text-align: center;
      }
    }
  </style>