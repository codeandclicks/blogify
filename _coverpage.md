<!-- _coverpage.md -->

<div class="cover-hero">
  <div class="cover-logo">💻</div>
  <h1 class="cover-title">Code & Clicks</h1>
  <p class="cover-tagline">Master Software Development, Testing & Automation</p>
  
  <div class="cover-features">
    <div class="feature-item">
      <span class="feature-icon">☕</span>
      <span class="feature-text">Java & OOP</span>
    </div>
    <div class="feature-item">
      <span class="feature-icon">🎭</span>
      <span class="feature-text">Playwright</span>
    </div>
    <div class="feature-item">
      <span class="feature-icon">🔧</span>
      <span class="feature-text">Selenium</span>
    </div>
    <div class="feature-item">
      <span class="feature-icon">🚀</span>
      <span class="feature-text">API Testing</span>
    </div>
  </div>

  <div class="cover-buttons">
    <a href="#/README" class="cover-btn cover-btn-primary">Get Started →</a>
    <a href="#/blogs/javascript/Introduction" class="cover-btn cover-btn-secondary">Learn JavaScript</a>
  </div>

  <div class="cover-highlight">
    <span class="highlight-badge">🔥 New Series</span>
    <span class="highlight-text">Mastering Playwright Automation with JavaScript</span>
  </div>
</div>

<style>
  .cover-hero {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 40px 20px;
    text-align: center;
  }

  .cover-logo {
    font-size: 5rem;
    margin-bottom: 20px;
    animation: float 3s ease-in-out infinite;
  }

  @keyframes float {
    0%, 100% { transform: translateY(0px); }
    50% { transform: translateY(-20px); }
  }

  .cover-title {
    font-size: 4rem !important;
    font-weight: 800 !important;
    margin: 0 0 15px 0 !important;
    background: linear-gradient(135deg, #ffffff 0%, #e0e7ff 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    letter-spacing: -0.02em;
  }

  .cover-tagline {
    font-size: 1.5rem;
    color: rgba(255, 255, 255, 0.9);
    margin-bottom: 40px;
    font-weight: 300;
    letter-spacing: 0.5px;
  }

  .cover-features {
    display: flex;
    gap: 20px;
    flex-wrap: wrap;
    justify-content: center;
    margin-bottom: 40px;
    padding: 0 20px;
  }

  .feature-item {
    display: flex;
    align-items: center;
    gap: 8px;
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    padding: 12px 24px;
    border-radius: 50px;
    border: 1px solid rgba(255, 255, 255, 0.2);
    transition: all 0.3s ease;
  }

  .feature-item:hover {
    background: rgba(255, 255, 255, 0.2);
    transform: translateY(-4px);
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
  }

  .feature-icon {
    font-size: 1.5rem;
  }

  .feature-text {
    font-size: 1rem;
    font-weight: 600;
    color: white;
  }

  .cover-buttons {
    display: flex;
    gap: 20px;
    margin-bottom: 50px;
    flex-wrap: wrap;
    justify-content: center;
  }

  .cover-btn {
    padding: 16px 40px;
    font-size: 1.1rem;
    font-weight: 600;
    border-radius: 50px;
    text-decoration: none;
    transition: all 0.3s ease;
    display: inline-block;
    border: 2px solid transparent;
  }

  .cover-btn-primary {
    background: white;
    color: #4f46e5;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.2);
  }

  .cover-btn-primary:hover {
    transform: translateY(-4px);
    box-shadow: 0 12px 32px rgba(0, 0, 0, 0.3);
    background: #f0f0f0;
  }

  .cover-btn-secondary {
    background: transparent;
    color: white;
    border: 2px solid white;
  }

  .cover-btn-secondary:hover {
    background: white;
    color: #4f46e5;
    transform: translateY(-4px);
  }

  .cover-highlight {
    display: flex;
    align-items: center;
    gap: 15px;
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    padding: 16px 32px;
    border-radius: 50px;
    border: 1px solid rgba(255, 255, 255, 0.2);
    animation: pulse 2s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { 
      opacity: 0.8; 
      box-shadow: 0 0 0 0 rgba(255, 255, 255, 0.3);
    }
    50% { 
      opacity: 1; 
      box-shadow: 0 0 0 10px rgba(255, 255, 255, 0);
    }
  }

  .highlight-badge {
    background: linear-gradient(135deg, #ef4444, #dc2626);
    padding: 6px 14px;
    border-radius: 20px;
    font-size: 0.9rem;
    font-weight: 700;
    color: white;
  }

  .highlight-text {
    font-size: 1.1rem;
    font-weight: 600;
    color: white;
  }

  /* Responsive Design */
  @media screen and (max-width: 768px) {
    .cover-logo {
      font-size: 3.5rem;
    }

    .cover-title {
      font-size: 2.5rem !important;
    }

    .cover-tagline {
      font-size: 1.2rem;
    }

    .cover-features {
      gap: 10px;
    }

    .feature-item {
      padding: 10px 18px;
    }

    .feature-text {
      font-size: 0.9rem;
    }

    .cover-buttons {
      flex-direction: column;
      width: 100%;
      padding: 0 20px;
    }

    .cover-btn {
      width: 100%;
      text-align: center;
    }

    .cover-highlight {
      flex-direction: column;
      gap: 10px;
      padding: 16px 24px;
    }

    .highlight-text {
      font-size: 1rem;
    }
  }
</style>
