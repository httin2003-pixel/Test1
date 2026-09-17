<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no">
<title>Siêu Cấp Thế Giới Trung Cổ II Alpha 2 - Remastered</title>
<style>
html,body{margin:0;overflow:hidden;background:#111;user-select:none;}
#hud{position:absolute;top:15px;left:15px;color:#fff;background:rgba(20,20,35,0.75);backdrop-filter:blur(5px);padding:12px 18px;border-radius:12px;font-family:'Segoe UI',Roboto,sans-serif;font-size:14px;line-height:1.6;border:1px solid rgba(255,255,255,0.15);box-shadow:0 8px 32px rgba(0,0,0,0.3);z-index:10}
#joy{position:absolute;left:25px;bottom:25px;width:120px;height:120px;border-radius:60px;background:rgba(255,255,255,0.1);border:2px solid rgba(255,255,255,0.2);touch-action:none;z-index:10}
#stick{position:absolute;left:40px;top:40px;width:40px;height:40px;border-radius:20px;background:rgba(255,255,255,0.8);box-shadow:0 0 10px rgba(255,255,255,0.5)}
#attack{position:absolute;right:25px;bottom:35px;width:85px;height:85px;border-radius:43px;border:2px solid rgba(255,255,255,0.3);background:linear-gradient(135deg, #e63946, #d62828);color:#fff;font-size:32px;box-shadow:0 6px 20px rgba(214,40,40,0.5);z-index:10;transition:transform 0.1s}
#attack:active{transform:scale(0.9)}
#overlay{position:absolute;inset:0;display:none;align-items:center;justify-content:center;flex-direction:column;background:rgba(10,10,15,0.85);backdrop-filter:blur(8px);color:#fff;z-index:20;font-family:'Segoe UI',Roboto,sans-serif}
#overlay h1{font-size:42px;margin-bottom:20px;text-shadow:0 0 10px rgba(255,255,255,0.3)}
button.btn{cursor:pointer;padding:12px 30px;background:#4cc9f0;border:none;border-radius:25px;color:#000;font-weight:bold;font-size:16px;box-shadow:0 4px 15px rgba(76,201,240,0.4)}
</style>
</head>
<body>
<div id="hud">Loading...</div>
<div id="joy"><div id="stick"></div></div>
<button id="attack">⚔</button>
<div id="overlay"><h1 id="msg"></h1><button class="btn" onclick="location.reload()">Chơi lại</button></div>

<script type="module">
import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js';

const scene = new THREE.Scene();
scene.background = new THREE.Color(0x87ceeb);
scene.fog = new THREE.FogExp2(0x87ceeb, 0.015);

const camera = new THREE.PerspectiveCamera(60, innerWidth / innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer({ antialias: true, powerPreference: "high-performance" });
renderer.setSize(innerWidth, innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.1;
document.body.appendChild(renderer.domElement);

// --- ÁNH SÁNG ---
const ambientLight = new THREE.AmbientLight(0xdbe9f5, 0.6);
scene.add(ambientLight);

const sun = new THREE.DirectionalLight(0xfffaed, 1.8);
sun.position.set(40, 60, 30);
sun.castShadow = true;
sun.shadow.mapSize.width = 2048;
sun.shadow.mapSize.height = 2048;
sun.shadow.camera.near = 0.5;
sun.shadow.camera.far = 150;
const d = 40;
sun.shadow.camera.left = -d;
sun.shadow.camera.right = d;
sun.shadow.camera.top = d;
sun.shadow.camera.bottom = -d;
sun.shadow.bias = -0.0005;
scene.add(sun);

// --- MẶT ĐẤT ---
const groundGeo = new THREE.PlaneGeometry(300, 300);
const groundMat = new THREE.MeshStandardMaterial({ color: 0x4e7c38, roughness: 0.9, metalness: 0.1 });
const ground = new THREE.Mesh(groundGeo, groundMat);
ground.rotation.x = -Math.PI / 2;
ground.receiveShadow = true;
scene.add(ground);

// --- CÂY CỐI CẢI TIẾN ---
function tree(x, z) {
  const g = new THREE.Group();
  const trunkGeo = new THREE.CylinderGeometry(0.25, 0.4, 3, 8);
  const trunkMat = new THREE.MeshStandardMaterial({ color: 0x4a2e18, roughness: 0.9 });
  const t = new THREE.Mesh(trunkGeo, trunkMat);
  t.position.y = 1.5;
  t.castShadow = true;
  t.receiveShadow = true;
  g.add(t);

  const leafMat = new THREE.MeshStandardMaterial({ color: 0x2d5a27, roughness: 0.6, flatShading: true });
  const levels = [
    { radius: 1.8, height: 2.2, y: 3.2 },
    { radius: 1.4, height: 1.8, y: 4.4 },
    { radius: 0.9, height: 1.4, y: 5.4 }
  ];
  levels.forEach(l => {
    const leaf = new THREE.Mesh(new THREE.ConeGeometry(l.radius, l.height, 7), leafMat);
    leaf.position.y = l.y;
    leaf.castShadow = true;
    leaf.receiveShadow = true;
    g.add(leaf);
  });

  g.position.set(x, 0, z);
  scene.add(g);
}
for (let i = 0; i < 70; i++) tree(Math.random() * 200 - 100, Math.random() * 200 - 100);

// --- NHÀ CỬA CẢI TIẾN ---
function house(x, z) {
  const g = new THREE.Group();
  const wallMat = new THREE.MeshStandardMaterial({ color: 0x8d735b, roughness: 0.8 });
  const roofMat = new THREE.MeshStandardMaterial({ color: 0x7a2918, roughness: 0.5 });
  const woodMat = new THREE.MeshStandardMaterial({ color: 0x3d2314, roughness: 0.9 });

  const body = new THREE.Mesh(new THREE.BoxGeometry(4.5, 3, 4.5), wallMat);
  body.position.y = 1.5;
  body.castShadow = true;
  body.receiveShadow = true;

  const roof = new THREE.Mesh(new THREE.ConeGeometry(3.8, 2.2, 4), roofMat);
  roof.position.y = 4.1;
  roof.rotation.y = Math.PI / 4;
  roof.castShadow = true;

  const door = new THREE.Mesh(new THREE.BoxGeometry(1, 1.8, 0.1), woodMat);
  door.position.set(0, 0.9, 2.26);

  g.add(body, roof, door);
  g.position.set(x, 0, z);
  scene.add(g);
}
house(10, 10); house(-10, 10); house(15, -12);

// --- NHÂN VẬT (KNIGHT) ---
function createKnight() {
  const g = new THREE.Group();
  const armorMat = new THREE.MeshStandardMaterial({ color: 0x8a9ba8, metalness: 0.8, roughness: 0.3 });
  const goldMat = new THREE.MeshStandardMaterial({ color: 0xdfa000, metalness: 0.9, roughness: 0.2 });
  const skinMat = new THREE.MeshStandardMaterial({ color: 0xe0ac69, roughness: 0.7 });

  const body = new THREE.Mesh(new THREE.BoxGeometry(0.8, 1.1, 0.5), armorMat);
  body.position.y = 1.15;
  body.castShadow = true;

  const head = new THREE.Mesh(new THREE.SphereGeometry(0.32, 12, 12), armorMat);
  head.position.y = 1.95;
  head.castShadow = true;

  const visor = new THREE.Mesh(new THREE.BoxGeometry(0.35, 0.1, 0.2), goldMat);
  visor.position.set(0, 1.98, 0.22);

  const shield = new THREE.Mesh(new THREE.BoxGeometry(0.1, 0.8, 0.5), armorMat);
  shield.position.set(-0.5, 1.1, 0.1);
  shield.castShadow = true;

  const swordGroup = new THREE.Group();
  const blade = new THREE.Mesh(new THREE.BoxGeometry(0.06, 1.2, 0.15), new THREE.MeshStandardMaterial({ color: 0xdddddd, metalness: 0.9, roughness: 0.1 }));
  blade.position.y = 0.6;
  blade.castShadow = true;
  const hilt = new THREE.Mesh(new THREE.BoxGeometry(0.1, 0.1, 0.3), goldMat);
  swordGroup.add(blade, hilt);
  swordGroup.position.set(0.5, 1.0, 0.2);
  swordGroup.rotation.x = Math.PI / 4;

  g.add(body, head, visor, shield, swordGroup);
  return g;
}

// --- QUÁI VẬT (GOBLIN) ---
function createGoblin() {
  const g = new THREE.Group();
  const skinMat = new THREE.MeshStandardMaterial({ color: 0x3a7d32, roughness: 0.8 });
  const clothMat = new THREE.MeshStandardMaterial({ color: 0x4a3525, roughness: 0.9 });

  const body = new THREE.Mesh(new THREE.BoxGeometry(0.7, 0.9, 0.45), clothMat);
  body.position.y = 0.95;
  body.castShadow = true;

  const head = new THREE.Mesh(new THREE.SphereGeometry(0.38, 10, 10), skinMat);
  head.position.y = 1.65;
  head.castShadow = true;

  const earL = new THREE.Mesh(new THREE.ConeGeometry(0.12, 0.4, 4), skinMat);
  earL.rotation.z = Math.PI / 3;
  earL.position.set(0.38, 1.75, 0);
  const earR = earL.clone();
  earR.rotation.z = -Math.PI / 3;
  earR.position.set(-0.38, 1.75, 0);

  const eyeMat = new THREE.MeshBasicMaterial({ color: 0xff0000 });
  const eyeL = new THREE.Mesh(new THREE.SphereGeometry(0.06, 6, 6), eyeMat);
  eyeL.position.set(0.12, 1.72, 0.32);
  const eyeR = eyeL.clone();
  eyeR.position.set(-0.12, 1.72, 0.32);

  g.add(body, head, earL, earR, eyeL, eyeR);
  g.userData = { hp: 40, maxHp: 40, dead: false, rewarded: false, xpReward: 25 };
  return g;
}

const player = createKnight();
scene.add(player);

const stats = { hp: 100, maxHp: 100, xp: 0, level: 1, next: 100, gold: 0, damage: 15 };
const enemies = [];
const loot = [];

function spawnGoblin() {
  const e = createGoblin();
  e.position.set(Math.random() * 60 - 30, 0, Math.random() * 60 - 30);
  scene.add(e);
  enemies.push(e);
}
for (let i = 0; i < 8; i++) spawnGoblin();

let joyX = 0, joyY = 0;
const joy = document.getElementById('joy'), stick = document.getElementById('stick');
joy.addEventListener('touchmove', e => {
  e.preventDefault();
  const r = joy.getBoundingClientRect(), t = e.touches[0];
  let dx = t.clientX - (r.left + r.width / 2), dy = t.clientY - (r.top + r.height / 2);
  const m = Math.hypot(dx, dy), lim = 35;
  if (m > lim) { dx = dx / m * lim; dy = dy / m * lim; }
  joyX = dx / lim; joyY = dy / lim;
  stick.style.left = (40 + dx) + 'px'; stick.style.top = (40 + dy) + 'px';
}, { passive: false });
joy.addEventListener('touchend', () => { joyX = joyY = 0; stick.style.left = '40px'; stick.style.top = '40px'; });

function dropGold(pos) {
  const c = new THREE.Mesh(
    new THREE.CylinderGeometry(0.25, 0.25, 0.08, 12),
    new THREE.MeshStandardMaterial({ color: 0xffd700, metalness: 0.9, roughness: 0.2 })
  );
  c.rotation.x = Math.PI / 3;
  c.position.copy(pos);
  c.position.y = 0.3;
  c.castShadow = true;
  c.userData = { value: 5 + Math.floor(Math.random() * 10) };
  scene.add(c);
  loot.push(c);
}

function enemyDie(e) {
  if (e.userData.rewarded) return;
  e.userData.rewarded = true;
  e.userData.dead = true;
  e.userData.deathTime = performance.now();
  stats.xp += e.userData.xpReward;
  dropGold(e.position);
  e.rotation.z = Math.PI / 2;
  e.position.y = 0.2;
}

document.getElementById('attack').onclick = () => {
  for (const e of enemies) {
    if (e.userData.dead) continue;
    if (player.position.distanceTo(e.position) < 3) {
      e.userData.hp -= stats.damage;
      if (e.userData.hp <= 0) enemyDie(e);
    }
  }
};

function levelCheck() {
  if (stats.xp >= stats.next) {
    stats.xp -= stats.next;
    stats.level++;
    stats.next = Math.floor(stats.next * 1.5);
    stats.maxHp += 20;
    stats.hp = stats.maxHp;
    stats.damage += 5;
  }
}

function endGame(text) {
  document.getElementById('msg').textContent = text;
  document.getElementById('overlay').style.display = 'flex';
}

function hud() {
  document.getElementById('hud').innerHTML =
    `<b>HP:</b> ${Math.floor(stats.hp)} / ${stats.maxHp}<br>` +
    `<b>LV:</b> ${stats.level}<br>` +
    `<b>XP:</b> ${stats.xp} / ${stats.next}<br>` +
    `<b>GOLD:</b> ${stats.gold}`;
}

function animate() {
  requestAnimationFrame(animate);

  const speed = 0.14;
  if (joyX !== 0 || joyY !== 0) {
    player.position.x += joyX * speed;
    player.position.z += joyY * speed;
    player.rotation.y = Math.atan2(-joyX, -joyY);
  }

  const now = performance.now();

  for (let i = enemies.length - 1; i >= 0; i--) {
    const e = enemies[i];

    if (e.userData.dead) {
      if (now - e.userData.deathTime > 5000) {
        scene.remove(e);
        enemies.splice(i, 1);
        spawnGoblin();
      }
      continue;
    }

    const dir = new THREE.Vector3().subVectors(player.position, e.position);
    const d = dir.length();

    if (d > 1) {
      dir.normalize();
      e.position.addScaledVector(dir, 0.035);
      e.rotation.y = Math.atan2(dir.x, dir.z);
    }
    if (d < 1.8) stats.hp -= 0.04;
  }

  for (let i = loot.length - 1; i >= 0; i--) {
    const item = loot[i];
    item.rotation.z += 0.03;
    if (player.position.distanceTo(item.position) < 2) {
      stats.gold += item.userData.value;
      scene.remove(item);
      loot.splice(i, 1);
    }
  }

  levelCheck();

  if (stats.hp <= 0) endGame("GAME OVER");
  if (stats.level >= 5) endGame("VICTORY!");

  camera.position.set(player.position.x, 8, player.position.z + 10);
  camera.lookAt(player.position.x, player.position.y + 1, player.position.z);

  hud();
  renderer.render(scene, camera);
}
animate();

addEventListener('resize', () => {
  camera.aspect = innerWidth / innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth, innerHeight);
});
</script>
</body>
</html>
