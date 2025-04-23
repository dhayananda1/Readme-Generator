<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="Generate a professional GitHub README for your profile with ease.">
  <title>GitHub README Generator</title>
  <style>
    body {
      margin: 0;
      font-family: 'Arial', sans-serif;
      background: #1a1a2e;
      color: #e6e6fa;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      overflow-x: hidden;
    }
    #canvas {
      position: fixed;
      top: 0;
      left: 0;
      z-index: -1;
    }
    .container {
      background: rgba(255, 255, 255, 0.1);
      backdrop-filter: blur(10px);
      padding: 2rem;
      border-radius: 15px;
      max-width: 900px;
      width: 90%;
      box-shadow: 0 4px 30px rgba(0, 0, 0, 0.5);
      display: flex;
      flex-wrap: wrap;
      gap: 2rem;
    }
    .form, .preview {
      flex: 1;
      min-width: 300px;
    }
    h1 {
      text-align: center;
      color: #00ff88;
      width: 100%;
      font-size: 2.5rem;
      margin-bottom: 1rem;
    }
    label {
      display: block;
      margin: 0.5rem 0 0.2rem;
      font-size: 1.1rem;
    }
    input, textarea {
      width: 100%;
      padding: 0.8rem;
      border: none;
      border-radius: 5px;
      background: #2e2e4e;
      color: #e6e6fa;
      font-size: 1rem;
      resize: vertical;
    }
    button {
      background: #00ff88;
      color: #1a1a2e;
      padding: 0.8rem 1.5rem;
      border: none;
      border-radius: 5px;
      font-size: 1.1rem;
      cursor: pointer;
      margin-top: 1rem;
      transition: background 0.3s;
    }
    button:hover {
      background: #00cc66;
    }
    .preview {
      background: #0f0f23;
      padding: 1.5rem;
      border-radius: 10px;
      overflow-x: auto;
    }
    pre {
      white-space: pre-wrap;
      font-size: 0.9rem;
      color: #e6e6fa;
    }
    @media (max-width: 768px) {
      .container {
        flex-direction: column;
        margin: 1rem;
      }
    }
  </style>
</head>
<body>
  <canvas id="canvas"></canvas>
  <div class="container">
    <h1>GitHub README Generator</h1>
    <div class="form">
      <label for="name">Name</label>
      <input type="text" id="name" placeholder="e.g., Dayananda" oninput="generateReadme()">
      <label for="bio">Short Bio</label>
      <textarea id="bio" placeholder="e.g., Passionate Java Developer and Web Enthusiast" oninput="generateReadme()"></textarea>
      <label for="skills">Skills (comma-separated)</label>
      <input type="text" id="skills" placeholder="e.g., Java, JavaScript, WordPress, Three.js" oninput="generateReadme()">
      <label for="collaborate">Looking to Collaborate On</label>
      <textarea id="collaborate" placeholder="e.g., WordPress projects, 3D web apps, Java projects" oninput="generateReadme()"></textarea>
      <label for="funfact">Fun Fact</label>
      <input type="text" id="funfact" placeholder="e.g., Built a 3D Snake game!" oninput="generateReadme()">
      <label for="links">Links (e.g., Portfolio, LinkedIn)</label>
      <textarea id="links" placeholder="e.g., Portfolio: [link], LinkedIn: [link]" oninput="generateReadme()"></textarea>
      <button onclick="downloadReadme()">Download README.md</button>
    </div>
    <div class="preview">
      <h2>Preview</h2>
      <pre id="readme-preview"></pre>
    </div>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
  <script>
    // Three.js Background Animation (Floating Particles)
    const canvas = document.getElementById('canvas');
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ canvas, alpha: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.position.z = 50;

    const particles = [];
    const particleCount = 100;
    const geometry = new THREE.SphereGeometry(0.2, 16, 16);
    const material = new THREE.MeshBasicMaterial({ color: 0x00ff88 });

    for (let i = 0; i < particleCount; i++) {
      const particle = new THREE.Mesh(geometry, material);
      particle.position.set(
        (Math.random() - 0.5) * 100,
        (Math.random() - 0.5) * 100,
        (Math.random() - 0.5) * 100
      );
      scene.add(particle);
      particles.push(particle);
    }

    function animate() {
      requestAnimationFrame(animate);
      particles.forEach(p => {
        p.position.x += (Math.random() - 0.5) * 0.05;
        p.position.y += (Math.random() - 0.5) * 0.05;
        p.position.z += (Math.random() - 0.5) * 0.05;
      });
      renderer.render(scene, camera);
    }
    animate();
    window.addEventListener('resize', () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });

    // README Generator Logic
    function generateReadme() {
      const name = document.getElementById('name').value || 'Your Name';
      const bio = document.getElementById('bio').value || 'Passionate developer';
      const skills = document.getElementById('skills').value.split(',').map(s => s.trim()).filter(s => s);
      const collaborate = document.getElementById('collaborate').value || 'Exciting projects';
      const funfact = document.getElementById('funfact').value || 'I love coding!';
      const links = document.getElementById('links').value || 'Portfolio: [link]';

      const skillsList = skills.length ? skills.map(s => `- **${s}**`).join('\n') : '- Add your skills!';
      const readme = `# 👋 Hi, I'm ${name}!

${bio}

## 🌟 My Skills
${skillsList}

## 🔍 I'm Looking to Collaborate On
${collaborate}

## 💡 Fun Fact
${funfact}

## 📫 Let's Connect
${links}

Happy coding! 🚀`;

      document.getElementById('readme-preview').textContent = readme;
      return readme;
    }

    function downloadReadme() {
      const readme = generateReadme();
      const blob = new Blob([readme], { type: 'text/markdown' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = 'README.md';
      a.click();
      URL.revokeObjectURL(url);
    }

    // Initial preview
    generateReadme();
  </script>
</body>
</html>
