<script>
    import { onMount } from "svelte";
    import * as THREE from "three";
    import asteroidTexture from "../assets/asteroid_texture.jpg";

    let canvas;

    onMount(() => {
        // Basic Three.js setup
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(
            75,
            window.innerWidth / window.innerHeight,
            0.1,
            1000,
        );
        const renderer = new THREE.WebGLRenderer({ canvas });
        renderer.setSize(window.innerWidth, window.innerHeight);

        // Add lighting
        const ambientLight = new THREE.AmbientLight(0x404040); // Soft white light
        scene.add(ambientLight);

        const pointLight = new THREE.PointLight(0xffffff, 1, 100);
        pointLight.position.set(50, 50, 50);
        scene.add(pointLight);

        // Load texture for the celestial body
        const textureLoader = new THREE.TextureLoader();
        const celestialTexture = textureLoader.load(asteroidTexture); // Adjust path as necessary

        const celestialGeometry = new THREE.SphereGeometry(5, 32, 32);
        const celestialMaterial = new THREE.MeshBasicMaterial({
            map: celestialTexture,
        });
        const celestialBody = new THREE.Mesh(
            celestialGeometry,
            celestialMaterial,
        );
        celestialBody.position.set(0, 0, -50);
        scene.add(celestialBody);

        // Adding stars to the scene
        const starGeometry = new THREE.BufferGeometry();
        const starVertices = [];
        for (let i = 0; i < 10000; i++) {
            const x = Math.random() * 2000 - 1000;
            const y = Math.random() * 2000 - 1000;
            const z = Math.random() * 2000 - 1000;
            starVertices.push(x, y, z);
        }
        starGeometry.setAttribute(
            "position",
            new THREE.Float32BufferAttribute(starVertices, 3),
        );
        const starMaterial = new THREE.PointsMaterial({
            color: 0xffffff,
            size: 1,
        });
        const stars = new THREE.Points(starGeometry, starMaterial);
        scene.add(stars);

        camera.position.z = 100;

        // Mouse interaction
        document.addEventListener("mousemove", (event) => {
            const mouseX = (event.clientX / window.innerWidth) * 2 - 1;
            const mouseY = -(event.clientY / window.innerHeight) * 2 + 1;
            camera.position.x = mouseX * 50;
            camera.position.y = mouseY * 50;
            camera.lookAt(scene.position);
        });

        function animate() {
            requestAnimationFrame(animate);
            celestialBody.rotation.y += 0.01; // Rotate the Celestial Body
            renderer.render(scene, camera);
        }
        animate();

        // Resize handler
        window.addEventListener("resize", () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    });
</script>

<canvas bind:this={canvas}></canvas>
