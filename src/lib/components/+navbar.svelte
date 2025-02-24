<script>
  let menuOpen = false;
</script>

<nav class="navbar">
  <div class="container">
      <a href="/" class="logo">Tech Conference</a>
      <button class="menu-btn" on:click={() => (menuOpen = !menuOpen)}>
          {menuOpen ? '✖' : '☰'}
      </button>
      <ul class="nav-links {menuOpen ? 'open' : ''}">
          <li><a href="/">Home</a></li>
          <li><a href="/speakers">Speakers</a></li>
          <li><a href="/schedule">Schedule</a></li>
          <li><a href="/sponsors">Sponsors</a></li>
          <li><a href="/about">About</a></li>
          <li><a href="/contact">Contact</a></li>
      </ul>
  </div>
</nav>

<style>
  .navbar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 1rem 2rem;
      background: linear-gradient(45deg, #000428, #004e92); /* Matching Hero Gradient */
      color: white;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .container {
      display: flex;
      justify-content: space-between;
      align-items: center;
      width: 100%;
      max-width: 1200px;
      margin: 0 auto;
  }

  .logo {
      font-size: 1.8rem;
      font-weight: 600;
      text-decoration: none;
      color: white;
      transition: color 0.3s ease;
  }

  .logo:hover {
      color: #60efff; /* Lighter hover color */
  }

  .nav-links {
      list-style: none;
      display: flex;
      gap: 1.5rem;
      margin: 0;
      padding: 0;
  }

  .nav-links a {
      color: white;
      text-decoration: none;
      padding: 0.5rem 1rem;
      border-radius: 6px;
      transition: background-color 0.3s ease, color 0.3s ease;
  }

  .nav-links a:hover,
  .nav-links a:focus {
      background-color: rgba(255, 255, 255, 0.1);
      color: #60efff; /* Lighter hover color */
  }

  .menu-btn {
      display: none;
      background: none;
      border: none;
      color: white;
      font-size: 1.8rem;
      cursor: pointer;
  }

  @media (max-width: 768px) {
      .nav-links {
          display: none;
          flex-direction: column;
          position: absolute;
          top: 60px;
          left: 0;
          width: 100%;
          background: rgba(0, 0, 0, 0.8); /* Darker, slightly transparent background for mobile menu */
          text-align: center;
          padding: 1rem 0;
          z-index: 100;
      }

      .nav-links.open {
          display: flex;
      }

      .nav-links li {
          padding: 0.5rem 0;
      }

      .menu-btn {
          display: block;
      }
  }
</style>