import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.158/build/three.module.js';
import { FontLoader } from 'https://cdn.jsdelivr.net/npm/three@0.158/examples/jsm/loaders/FontLoader.js';
import { TextGeometry } from 'https://cdn.jsdelivr.net/npm/three@0.158/examples/jsm/geometries/TextGeometry.js';

// 🎬 SCENE
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, innerWidth/innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer({ antialias: true });

renderer.setSize(innerWidth, innerHeight);
document.body.appendChild(renderer.domElement);
camera.position.z = 5;

// 🎵 MUSIC
const music = document.getElementById("music");
const startBtn = document.getElementById("startBtn");

startBtn.onclick = () => {
  music.play();
  startBtn.style.display = "none";
  startExperience();
};

// 💌 TYPEWRITER
function typeWriter(text, speed=40) {
  let i = 0;
  const el = document.getElementById("message");
  el.innerHTML = "";

  function typing() {
    if (i < text.length) {
      el.innerHTML += text.charAt(i);
      i++;
      setTimeout(typing, speed);
    }
  }
  typing();
}

// 📸 TEXTURES
const images = ["img1.jpg","img2.jpg","img3.jpg"];
let textures = images.map(img => new THREE.TextureLoader().load(img));
let currentTexture = 0;

// ❤️ HEART
let geo = new THREE.BufferGeometry();
let pos = [];

for (let i=0;i<2500;i++){
  let t = Math.random()*Math.PI*2;
  let x = 16*Math.pow(Math.sin(t),3);
  let y = 13*Math.cos(t)-5*Math.cos(2*t)-2*Math.cos(3*t)-Math.cos(4*t);
  let z = (Math.random()-0.5)*5;
  pos.push(x*0.1,y*0.1,z);
}

geo.setAttribute('position', new THREE.Float32BufferAttribute(pos,3));

let mat = new THREE.PointsMaterial({
  size:0.05,
  map:textures[0],
  transparent:true
});

let heart = new THREE.Points(geo,mat);
scene.add(heart);

// 🌌 GALAXY
let starsGeo = new THREE.BufferGeometry();
let stars = [];

for (let i=0;i<6000;i++){
  stars.push(
    (Math.random()-0.5)*200,
    (Math.random()-0.5)*200,
    (Math.random()-0.5)*200
  );
}

starsGeo.setAttribute('position', new THREE.Float32BufferAttribute(stars,3));
let galaxy = new THREE.Points(starsGeo,new THREE.PointsMaterial({size:0.7}));

// ✨ NAME FROM STARS
let namePoints;

function createName() {
  const loader = new FontLoader();
  loader.load('helvetiker_regular.typeface.json', (font) => {

    const textGeo = new TextGeometry('JIGYASA', {
      font: font,
      size: 1,
      height: 0.1
    });

    textGeo.center();

    const pointsGeo = new THREE.BufferGeometry();
    const vertices = [];

    const posAttr = textGeo.attributes.position;

    for (let i = 0; i < posAttr.count; i++) {
      vertices.push(
        posAttr.getX(i),
        posAttr.getY(i),
        posAttr.getZ(i)
      );
    }

    pointsGeo.setAttribute('position', new THREE.Float32BufferAttribute(vertices,3));

    const mat = new THREE.PointsMaterial({ size: 0.05 });
    namePoints = new THREE.Points(pointsGeo, mat);

    scene.add(namePoints);
  });
}

// 🎮 BUTTON
const btn = document.getElementById("btn");

btn.onmouseover = () => {
  btn.style.position="absolute";
  btn.style.top=Math.random()*80+"%";
  btn.style.left=Math.random()*80+"%";
};

btn.onclick = () => {
  typeWriter("I love you Jigyasa ❤️ — Vayu");
};

// 🎬 STORY FLOW
function startExperience(){

  const msgs = [
    "Hey Jigyasa...",
    "I didn’t plan this",
    "But somehow everything led here",
    "And I just wanted to say..."
  ];

  msgs.forEach((m,i)=>{
    setTimeout(()=>typeWriter(m),2000*(i+1));
  });

  // slideshow
  setInterval(()=>{
    mat.map = textures[currentTexture++%textures.length];
    mat.needsUpdate = true;
  },3000);

  // heart grows
  setTimeout(()=>{
    heart.scale.multiplyScalar(1.5);
  },6000);

  // transition
  setTimeout(()=>{
    scene.remove(heart);
    scene.add(galaxy);
    typeWriter("Look closer...");
  },9000);

  // NAME APPEARS
  setTimeout(()=>{
    createName();
    typeWriter("Jigyasa ❤️");
    btn.style.display="inline-block";
  },13000);
}

// 🎬 LOOP
function animate(){
  requestAnimationFrame(animate);

  heart.rotation.y += 0.01;
  galaxy.rotation.y += 0.0008;

  if(namePoints) namePoints.rotation.y += 0.005;

  renderer.render(scene,camera);
}
animate();