<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  import { onMount } from 'svelte';
  
  const dispatch = createEventDispatcher();
  
  let isLaunching = false;
  let countdown = 5;
  let showRocket = false;
  let rocketPosition = { x: 50, y: 100 };
  let countdownInterval: number;
  let rocketInterval: number;
  let particles: Array<{x: number, y: number, vx: number, vy: number, life: number}> = [];
  let showWelcomeMessage = false;
  
  function startLaunch() {
    if (isLaunching) return;
    
    isLaunching = true;
    countdown = 5;
    
    // Start countdown
    countdownInterval = setInterval(() => {
      countdown--;
      if (countdown <= 0) {
        clearInterval(countdownInterval);
        startRocketAnimation();
      }
    }, 1000);
  }
  
  function startRocketAnimation() {
    showRocket = true;
    rocketPosition = { x: 50, y: 100 };
    
    // Create initial particles
    for (let i = 0; i < 50; i++) {
      particles.push({
        x: 50,
        y: 100,
        vx: (Math.random() - 0.5) * 4,
        vy: Math.random() * 2 + 1,
        life: 1
      });
    }
    
    // Animate rocket launch
    rocketInterval = setInterval(() => {
      rocketPosition.y -= 2;
      
      // Update particles
      particles = particles.map(particle => ({
        ...particle,
        x: particle.x + particle.vx,
        y: particle.y + particle.vy,
        life: particle.life - 0.02
      })).filter(particle => particle.life > 0);
      
      // Add new particles
      if (Math.random() < 0.3) {
        particles.push({
          x: rocketPosition.x + (Math.random() - 0.5) * 10,
          y: rocketPosition.y + 30,
          vx: (Math.random() - 0.5) * 3,
          vy: Math.random() * 2 + 0.5,
          life: 1
        });
      }
      
      if (rocketPosition.y < -20) {
        clearInterval(rocketInterval);
        showWelcomeMessage = true;
        // Redirect to Kiwi Sri Lankans website
        setTimeout(() => {
          window.location.href = 'https://kiwisrilankans.org/';
        }, 2500);
      }
    }, 50);
  }
  
  onMount(() => {
    return () => {
      if (countdownInterval) clearInterval(countdownInterval);
      if (rocketInterval) clearInterval(rocketInterval);
    };
  });
</script>

<div class="launch-container">
  {#if !isLaunching}
    <div class="launch-screen">
      <img src="/Logosnew.webp" alt="Kiwi Sri Lankans Logo" class="launch-logo" />
      <div class="launch-title">
        <h1>Welcome to Kiwi Sri Lankans</h1>
        <p>Connecting the Sri Lankan community across New Zealand</p>
      </div>
      <button class="launch-button" on:click={startLaunch}>
        <span class="launch-text">LAUNCH</span>
        <div class="launch-icon">🚀</div>
      </button>
      <div class="launch-description">
        <p>Click to explore events, services, and community connections</p>
      </div>
    </div>
  {:else if countdown > 0}
    <div class="countdown-overlay">
      <div class="countdown-number">{countdown}</div>
      <div class="countdown-text">Launching in...</div>
      <div class="countdown-progress">
        <div class="progress-bar" style="width: {(5 - countdown) * 20}%"></div>
      </div>
    </div>
  {:else}
    <div class="rocket-container">
      {#if showWelcomeMessage}
        <div class="welcome-message">
          <h1>Welcome to Kiwi SL Web Portal</h1>
        </div>
      {:else}
        <div 
          class="rocket" 
          style="left: {rocketPosition.x}%; top: {rocketPosition.y}%"
        >
          🚀
        </div>
        
        {#each particles as particle, i}
          <div 
            class="particle"
            style="
              left: {particle.x}%; 
              top: {particle.y}%; 
              opacity: {particle.life};
              transform: scale({particle.life});
            "
          ></div>
        {/each}
        
        <div class="launch-trail"></div>
        <div class="launch-flash"></div>
      {/if}
    </div>
  {/if}
</div>

<style lang="scss">
  .launch-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    z-index: 1000;
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(135deg, #1e1e28 0%, #241c2b 100%);
    backdrop-filter: blur(10px);
  }
  
  .launch-screen {
    text-align: center;
    color: white;
    max-width: 600px;
    padding: 40px;
  }
  
  .launch-logo {
    width: 150px;
    margin-bottom: 40px;
    animation: fadeInLogo 1.5s ease-out;
  }

  @keyframes fadeInLogo {
    from {
      opacity: 0;
      transform: scale(0.8);
    }
    to {
      opacity: 1;
      transform: scale(1);
    }
  }
  
  .launch-title {
    margin-bottom: 60px;
    
    h1 {
      font-size: 48px;
      font-weight: bold;
      margin-bottom: 20px;
      background: linear-gradient(135deg, #639031, #FCBC45);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }
    
    p {
      font-size: 20px;
      opacity: 0.8;
      line-height: 1.6;
    }
  }
  
  .launch-button {
    background: linear-gradient(135deg, #639031, #FCBC45);
    border: none;
    border-radius: 50px;
    padding: 25px 50px;
    font-size: 28px;
    font-weight: bold;
    color: white;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 20px;
    box-shadow: 0 15px 40px rgba(99, 144, 49, 0.3);
    margin: 0 auto 40px;
    
    &:hover {
      transform: translateY(-8px);
      box-shadow: 0 20px 50px rgba(99, 144, 49, 0.5);
      background: linear-gradient(135deg, #7db53d, #639031);
    }
    
    &:active {
      transform: translateY(-4px);
    }
  }
  
  .launch-icon {
    font-size: 32px;
    animation: pulse 2s infinite;
  }
  
  .launch-description {
    p {
      font-size: 16px;
      opacity: 0.7;
      font-style: italic;
    }
  }
  
  @keyframes pulse {
    0%, 100% { transform: scale(1); }
    50% { transform: scale(1.1); }
  }
  
  .countdown-overlay {
    text-align: center;
    color: white;
  }
  
  .countdown-number {
    font-size: 150px;
    font-weight: bold;
    color: #639031;
    text-shadow: 0 0 50px rgba(99, 144, 49, 0.7);
    animation: countdownPulse 1s ease-in-out;
    margin-bottom: 20px;
  }
  
  .countdown-text {
    font-size: 28px;
    margin-bottom: 30px;
    opacity: 0.9;
  }
  
  .countdown-progress {
    width: 300px;
    height: 8px;
    background: rgba(255, 255, 255, 0.2);
    border-radius: 4px;
    margin: 0 auto;
    overflow: hidden;
    
    .progress-bar {
      height: 100%;
      background: linear-gradient(90deg, #639031, #FCBC45);
      border-radius: 4px;
      transition: width 1s ease;
    }
  }
  
  @keyframes countdownPulse {
    0% { transform: scale(0.3); opacity: 0; }
    50% { transform: scale(1.3); opacity: 1; }
    100% { transform: scale(1); opacity: 1; }
  }
  
  .rocket-container {
    position: relative;
    width: 100%;
    height: 100%;
    overflow: hidden;
  }
  
  .rocket {
    position: absolute;
    font-size: 80px;
    transition: all 0.05s linear;
    z-index: 10;
    filter: drop-shadow(0 0 20px rgba(99, 144, 49, 0.8));
    transform: translateX(-50%) rotate(-45deg);
  }
  
  .particle {
    position: absolute;
    width: 6px;
    height: 6px;
    background: radial-gradient(circle, #FCBC45, #639031);
    border-radius: 50%;
    z-index: 5;
  }
  
  .launch-trail {
    position: absolute;
    bottom: 0;
    left: 50%;
    width: 6px;
    height: 100vh;
    background: linear-gradient(to top, transparent, #639031, #FCBC45, white);
    transform: translateX(-50%);
    animation: trailFade 3s ease-out;
    box-shadow: 0 0 20px rgba(99, 144, 49, 0.5);
  }
  
  .launch-flash {
    position: absolute;
    bottom: 0;
    left: 50%;
    width: 200px;
    height: 200px;
    background: radial-gradient(circle, rgba(252, 188, 69, 0.8), transparent);
    transform: translateX(-50%);
    animation: flashFade 2s ease-out;
  }
  
  @keyframes trailFade {
    0% { opacity: 1; }
    100% { opacity: 0; }
  }
  
  @keyframes flashFade {
    0% { opacity: 1; transform: translateX(-50%) scale(0); }
    50% { opacity: 1; transform: translateX(-50%) scale(1); }
    100% { opacity: 0; transform: translateX(-50%) scale(1.5); }
  }
  
  .welcome-message {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
    color: white;
    
    h1 {
      font-size: 4vw;
      font-weight: bold;
      background: linear-gradient(135deg, #639031, #FCBC45);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      text-shadow: 0 0 30px rgba(99, 144, 49, 0.4);
      animation: fadeInWelcome 1.5s ease-out;
    }
  }

  @keyframes fadeInWelcome {
    from {
      opacity: 0;
      transform: translateY(30px) scale(0.9);
    }
    to {
      opacity: 1;
      transform: translateY(0) scale(1);
    }
  }
  
  @media (max-width: 768px) {
    .launch-logo {
      width: 120px;
      margin-bottom: 30px;
    }
    
    .launch-title h1 {
      font-size: 32px;
    }
    
    .launch-title p {
      font-size: 16px;
    }
    
    .launch-button {
      padding: 20px 40px;
      font-size: 24px;
    }
    
    .countdown-number {
      font-size: 100px;
    }
    
    .countdown-text {
      font-size: 20px;
    }

    .welcome-message h1 {
      font-size: 28px;
    }
  }
</style> 