<script>
    // --- SCENE SETUP ---
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({
        canvas: document.querySelector('#bg'),
        antialias: true,
    });

    renderer.setPixelRatio(window.devicePixelRatio);
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.position.setZ(30);

    // Initial render
    renderer.render(scene, camera);

    // --- CONTROLS ---
    const controls = new THREE.OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true; // Smooth movement
    controls.dampingFactor = 0.05;
    controls.screenSpacePanning = false;
    controls.maxDistance = 60;
    controls.minDistance = 15;
    controls.autoRotate = true; // Auto-rotate slowly
    controls.autoRotateSpeed = 0.5;

    // --- LIGHTING ---
    // Ambient Light (Soft overall lighting)
    const ambientLight = new THREE.AmbientLight(0x404040, 2);
    scene.add(ambientLight);

    // Directional Light (Main light source for shadows and definition)
    const directionalLight = new THREE.DirectionalLight(0xffffff, 1.5);
    directionalLight.position.set(5, 5, 5);
    scene.add(directionalLight);

    // Point Light (For luxury glow effect)
    const pointLight = new THREE.PointLight(0x06B6D4, 5, 100);
    pointLight.position.set(0, 5, 0);
    scene.add(pointLight);

    // --- GEOMETRY AND MATERIALS ---

    // Function to create a skill cube with text
    function createSkillCube(name, color, position) {
        const geometry = new THREE.BoxGeometry(4, 4, 4);
        const material = new THREE.MeshPhongMaterial({ 
            color: color, 
            shininess: 100,
            transparent: true,
            opacity: 0.95
        });
        const cube = new THREE.Mesh(geometry, material);

        // Add Text Label using Canvas Texture (since direct 3D text is complex)
        const canvas = document.createElement('canvas');
        const context = canvas.getContext('2d');
        canvas.width = 512;
        canvas.height = 512;

        context.fillStyle = '#0a0a0a';
        context.fillRect(0, 0, canvas.width, canvas.height);
        
        context.font = 'Bold 70px Inter';
        context.fillStyle = '#06B6D4';
        context.textAlign = 'center';
        context.fillText(name, canvas.width / 2, canvas.height / 2 + 25);
        
        const texture = new THREE.CanvasTexture(canvas);
        
        // Use the texture on the side of the cube
        const textMaterial = new THREE.MeshBasicMaterial({ map: texture, transparent: true });

        // Create a plane for the text
        const planeGeometry = new THREE.PlaneGeometry(4.5, 4.5);
        const plane = new THREE.Mesh(planeGeometry, textMaterial);
        
        // Attach plane to cube and position it slightly in front of one face
        plane.position.z = 2.01;
        cube.add(plane);
        
        // Set the cube position
        cube.position.set(position.x, position.y, position.z);
        scene.add(cube);
        
        return cube;
    }

    // Define skills and positions
    const skillCubes = [
        createSkillCube('Premiere Pro', 0xff0000, { x: -8, y: 0, z: 0 }),
        createSkillCube('After Effects', 0x9900ff, { x: 8, y: 0, z: 0 }),
        createSkillCube('CapCut Pro', 0x00ff00, { x: 0, y: 8, z: 0 }),
        createSkillCube('Photoshop', 0x00aaff, { x: 0, y: -8, z: 0 }),
        createSkillCube('Illustrator', 0xffaa00, { x: 0, y: 0, z: 8 }),
        createSkillCube('DaVinci Resolve', 0xffffff, { x: 0, y: 0, z: -8 }),
    ];


    // --- STARS BACKGROUND ---
    function addStar() {
        const geometry = new THREE.SphereGeometry(0.25, 24, 24);
        const material = new THREE.MeshStandardMaterial({ color: 0xffffff });
        const star = new THREE.Mesh(geometry, material);

        const [x, y, z] = Array(3).fill().map(() => THREE.MathUtils.randFloatSpread(100));

        star.position.set(x, y, z);
        scene.add(star);
    }
    
    // Add 200 stars
    Array(200).fill().forEach(addStar);
    
    // --- ANIMATION LOOP ---
    function animate() {
        requestAnimationFrame(animate);

        // Slow rotation for cubes (subtle movement even when user is not dragging)
        skillCubes.forEach(cube => {
            cube.rotation.x += 0.005;
            cube.rotation.y += 0.005;
        });

        controls.update(); // Required for auto-rotate and damping

        renderer.render(scene, camera);
    }

    // --- RESIZING HANDLER ---
    window.addEventListener('resize', () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    });
    
    // Start the animation loop when the window is fully loaded
    window.onload = animate; 
    
</script>
