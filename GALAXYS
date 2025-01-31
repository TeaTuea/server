// Load Three.js
const script1 = document.createElement("script");
script1.src = "https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js";
document.head.appendChild(script1);

// Load Simplex Noise
const script2 = document.createElement("script");
script2.src = "https://cdnjs.cloudflare.com/ajax/libs/simplex-noise/2.4.0/simplex-noise.min.js";
document.head.appendChild(script2);

script2.onload = function () {
    let scene, camera, renderer, points;
    let mouse = { x: 0, y: 0 };
    let simplex = new SimplexNoise();
    let mouseRadius = 0.5; // How big the area is around the mouse where particles turn white

    function init() {
        scene = new THREE.Scene();
        scene.background = new THREE.Color(0x000000); // Galaxy black background
        
        camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 3;

        renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Generate random brain-like point cloud
        let numParticles = 5000;
        let geometry = new THREE.BufferGeometry();
        let positions = new Float32Array(numParticles * 3);
        let colors = new Float32Array(numParticles * 3);

        for (let i = 0; i < numParticles; i++) {
            let theta = Math.random() * Math.PI * 2;
            let phi = Math.random() * Math.PI;
            let radius = 1.5 + Math.random() * 0.5;
            positions[i * 3] = radius * Math.sin(phi) * Math.cos(theta);
            positions[i * 3 + 1] = radius * Math.sin(phi) * Math.sin(theta);
            positions[i * 3 + 2] = radius * Math.cos(phi);

            // Color gradient: Yellow → Purple → Blue
            let t = i / numParticles;
            let color = new THREE.Color();
            if (t < 0.33) {
                color.setRGB(1, 1 - t * 3, 0); // Yellow to Yellowish Orange
            } else if (t < 0.66) {
                color.setRGB(1 - (t - 0.33) * 3, 0, 1); // Purple to Blue
            } else {
                color.setRGB(0.5 - (t - 0.66) * 2, 0, 1); // Blue to more Purple
            }

            colors[i * 3] = color.r;
            colors[i * 3 + 1] = color.g;
            colors[i * 3 + 2] = color.b;
        }

        geometry.setAttribute("position", new THREE.BufferAttribute(positions, 3));
        geometry.setAttribute("color", new THREE.BufferAttribute(colors, 3));

        let material = new THREE.PointsMaterial({
            vertexColors: true,
            size: 0.02,
            transparent: true
        });

        points = new THREE.Points(geometry, material);
        scene.add(points);

        animate();
        window.addEventListener("mousemove", onMouseMove);
        window.addEventListener("resize", onWindowResize);
    }

    function onMouseMove(event) {
        mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
        mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    }

    function onWindowResize() {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    }

    function animate() {
        requestAnimationFrame(animate);

        if (points) {
            let positions = points.geometry.attributes.position.array;
            let colors = points.geometry.attributes.color.array;

            for (let i = 0; i < positions.length / 3; i++) {
                let dx = positions[i * 3] - mouse.x * 2;
                let dy = positions[i * 3 + 1] - mouse.y * 2;
                let dist = Math.sqrt(dx * dx + dy * dy);

                let noise = simplex.noise3D(positions[i * 3], positions[i * 3 + 1], positions[i * 3 + 2]);
                positions[i * 3] += noise * 0.005;
                positions[i * 3 + 1] += noise * 0.005;

                // Add circular movement in the opposite direction (counterclockwise)
                let theta = Math.atan2(positions[i * 3 + 1], positions[i * 3]);
                let phi = Math.acos(positions[i * 3 + 2] / Math.sqrt(Math.pow(positions[i * 3], 2) + Math.pow(positions[i * 3 + 1], 2) + Math.pow(positions[i * 3 + 2], 2)));
                let speed = 0.002; // Slower speed of rotation

                // Reverse the direction (counterclockwise)
                theta -= speed;

                // Update positions based on new theta and phi
                let radius = Math.sqrt(Math.pow(positions[i * 3], 2) + Math.pow(positions[i * 3 + 1], 2) + Math.pow(positions[i * 3 + 2], 2));
                positions[i * 3] = radius * Math.sin(phi) * Math.cos(theta);
                positions[i * 3 + 1] = radius * Math.sin(phi) * Math.sin(theta);
                positions[i * 3 + 2] = radius * Math.cos(phi);

                // Make the radius of white bigger around the mouse
                if (dist < mouseRadius) {
                    colors[i * 3] = 1;
                    colors[i * 3 + 1] = 1;
                    colors[i * 3 + 2] = 1;
                } else {
                    // Revert to the original gradient color
                    let t = i / positions.length * 3;
                    let color = new THREE.Color();
                    if (t < 0.33) {
                        color.setRGB(1, 1 - t * 3, 0); // Yellow
                    } else if (t < 0.66) {
                        color.setRGB(1 - (t - 0.33) * 3, 0, 1); // Purple
                    } else {
                        color.setRGB(0.5 - (t - 0.66) * 2, 0, 1); // Blue
                    }
                    colors[i * 3] = color.r;
                    colors[i * 3 + 1] = color.g;
                    colors[i * 3 + 2] = color.b;
                }
            }

            points.geometry.attributes.position.needsUpdate = true;
            points.geometry.attributes.color.needsUpdate = true;
        }

        renderer.render(scene, camera);
    }

    init();
};
