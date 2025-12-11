<!DOCTYPE html>
<html lang="en">
<head>
    
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Deep Neural Network: Negative Feedback</title>
    <style>
        body {
            background-color: #121212;
            color: #e0e0e0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            margin: 0;
            padding: 20px;
        }

        h1 { margin-bottom: 5px; font-weight: 300; letter-spacing: 1px; }
        .subtitle { color: #777; font-size: 0.9em; margin-bottom: 20px; }
        
        .container {
            position: relative;
            box-shadow: 0 0 30px rgba(0,0,0,0.6);
            border-radius: 8px;
            overflow: hidden;
            background: #1e1e1e;
        }

        canvas { display: block; }

        .controls {
            margin-top: 20px;
            display: flex;
            gap: 40px;
            background: #252525;
            padding: 20px 40px;
            border-radius: 8px;
        }

        .control-group {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        label { margin-bottom: 8px; font-size: 0.85em; color: #bbb; text-transform: uppercase; letter-spacing: 1px;}

        input[type=range] {
            -webkit-appearance: none;
            width: 180px;
            height: 4px;
            background: #444;
            border-radius: 2px;
            outline: none;
        }

        input[type=range]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 16px;
            height: 16px;
            background: #00bcd4;
            border-radius: 50%;
            cursor: pointer;
            transition: transform .1s;
        }
        
        input[type=range]:active::-webkit-slider-thumb { transform: scale(1.2); }
        input[type=range].red-slider::-webkit-slider-thumb { background: #ff5252; }
    </style>
</head>
<body>

    <h1>Deep Negative Feedback</h1>
    <div class="subtitle">Input Layer &rarr; Hidden Layer &rarr; Output Layer (with Feedback)</div>

    <div class="container">
        <canvas id="netCanvas" width="900" height="500"></canvas>
    </div>

    <div class="controls">
        <div class="control-group">
            <label>Input Signal</label>
            <input type="range" id="inputSlider" min="0" max="100" value="0">
        </div>
        <div class="control-group">
            <label>Feedback (Inhibition)</label>
            <input type="range" id="feedbackSlider" class="red-slider" min="0" max="100" value="50">
        </div>
    </div>

    <script>
        const canvas = document.getElementById('netCanvas');
        const ctx = canvas.getContext('2d');
        const inputSlider = document.getElementById('inputSlider');
        const feedbackSlider = document.getElementById('feedbackSlider');

        // --- Configuration ---
        const particles = [];
        const maxGraphPoints = 200;
        const graphData = { output: [], hiddenAvg: [] };
        
        // Physics Params
        let inputStrength = 0;
        let feedbackWeight = 0.5;
        const reactionSpeed = 0.08;

        // --- Layer Definitions ---
        // We calculate positions dynamically based on canvas size
        const layers = {
            input: [
                { x: 100, y: 150, val: 0, label: "I1" },
                { x: 100, y: 350, val: 0, label: "I2" }
            ],
            hidden: [
                { x: 450, y: 100, val: 0, label: "H1" },
                { x: 450, y: 250, val: 0, label: "H2" },
                { x: 450, y: 400, val: 0, label: "H3" }
            ],
            output: [
                { x: 800, y: 250, val: 0, label: "Out" }
            ]
        };

        // Inputs
        inputSlider.addEventListener('input', (e) => inputStrength = e.target.value / 100);
        feedbackSlider.addEventListener('input', (e) => feedbackWeight = e.target.value / 100);

        // --- Particle System ---
        function spawnParticle(start, end, type) {
            // "Less quantiful": High threshold for spawning
            // We only spawn if source value is high enough
            if (Math.random() > 0.95) return; // Only 5% chance per frame
            
            particles.push({
                x: start.x,
                y: start.y,
                startX: start.x,
                startY: start.y,
                targetX: end.x,
                targetY: end.y,
                progress: 0,
                // Speed varies slightly for organic look
                speed: 0.015 + (Math.random() * 0.01), 
                type: type // 'excite' or 'inhibit'
            });
        }

        function updatePhysics() {
            // 1. INPUT LAYER
            // Simply adopts the slider value
            layers.input.forEach(node => {
                let target = inputStrength;
                node.val += (target - node.val) * reactionSpeed;
            });

            // 2. HIDDEN LAYER
            // Sum of inputs MINUS feedback from output
            let feedbackSignal = layers.output[0].val * feedbackWeight * 2.5;
            
            layers.hidden.forEach(hNode => {
                let incomingExcite = 0;
                // Sum inputs
                layers.input.forEach(iNode => incomingExcite += iNode.val);
                // Average inputs slightly so it doesn't explode
                incomingExcite = incomingExcite / 1.5; 

                // The Logic: Excitation - Inhibition
                let target = incomingExcite - feedbackSignal;
                if(target < 0) target = 0; // Rectified Linear Unit logic (ReLU-ish)

                hNode.val += (target - hNode.val) * reactionSpeed;

                // Spawn forward particles
                if(hNode.val > 0.1 && Math.random() < 0.05) {
                   spawnParticle(hNode, layers.output[0], 'excite');
                }
            });

            // 3. OUTPUT LAYER
            // Sum of hidden nodes
            let oNode = layers.output[0];
            let incomingHidden = 0;
            layers.hidden.forEach(hNode => incomingHidden += hNode.val);
            
            let oTarget = incomingHidden / 2.0; // Scale down slightly
            oNode.val += (oTarget - oNode.val) * reactionSpeed;

            // Spawn feedback particles (Output -> Hidden)
            if(oNode.val > 0.1 && feedbackWeight > 0.05) {
                // Randomly pick a hidden node to visualize sending feedback to
                let targetHidden = layers.hidden[Math.floor(Math.random() * layers.hidden.length)];
                // Even lower chance for feedback particles to keep visual clean
                if(Math.random() < 0.03) spawnParticle(oNode, targetHidden, 'inhibit');
            }

            // Spawn Input -> Hidden particles
            layers.input.forEach(iNode => {
                if(iNode.val > 0.1) {
                    let targetHidden = layers.hidden[Math.floor(Math.random() * layers.hidden.length)];
                    if(Math.random() < 0.03) spawnParticle(iNode, targetHidden, 'excite');
                }
            });

            // 4. PARTICLES
            for (let i = particles.length - 1; i >= 0; i--) {
                let p = particles[i];
                p.progress += p.speed;

                if (p.type === 'inhibit') {
                    // Curved path for feedback (Arcing up or down)
                    // Determine arc height based on Y distance
                    let midX = (p.startX + p.targetX) / 2;
                    let arcHeight = 150; 
                    // Flip arc if going to top or bottom nodes for visual spread
                    if(p.targetY < 250) arcHeight = -150;
                    
                    let midY = (p.startY + p.targetY) / 2 + arcHeight;
                    
                    let t = p.progress;
                    // Quadratic Bezier
                    p.x = (1-t)*(1-t)*p.startX + 2*(1-t)*t*midX + t*t*p.targetX;
                    p.y = (1-t)*(1-t)*p.startY + 2*(1-t)*t*midY + t*t*p.targetY;
                } else {
                    // Linear path for excitation
                    p.x = p.startX + (p.targetX - p.startX) * p.progress;
                    p.y = p.startY + (p.targetY - p.startY) * p.progress;
                }

                if (p.progress >= 1) particles.splice(i, 1);
            }

            // 5. GRAPH DATA
            graphData.output.push(oNode.val);
            // Calculate average hidden activation
            let avgH = layers.hidden.reduce((acc, n) => acc + n.val, 0) / layers.hidden.length;
            graphData.hiddenAvg.push(avgH);
            
            if(graphData.output.length > maxGraphPoints) {
                graphData.output.shift();
                graphData.hiddenAvg.shift();
            }
        }

        // --- Drawing ---

        function drawWire(start, end, type) {
            ctx.beginPath();
            if (type === 'inhibit') {
                ctx.strokeStyle = `rgba(255, 82, 82, ${0.1 + (feedbackWeight * 0.3)})`;
                ctx.lineWidth = 1 + (feedbackWeight * 2);
                
                // Curve calculation matches particle logic
                let midX = (start.x + end.x) / 2;
                let arcHeight = 150;
                if(end.y < 250) arcHeight = -150;
                let midY = (start.y + end.y) / 2 + arcHeight;
                
                ctx.moveTo(start.x, start.y);
                ctx.quadraticCurveTo(midX, midY, end.x, end.y);
            } else {
                // Forward wires (static opacity)
                ctx.strokeStyle = 'rgba(0, 188, 212, 0.15)';
                ctx.lineWidth = 1;
                ctx.moveTo(start.x, start.y);
                ctx.lineTo(end.x, end.y);
            }
            ctx.stroke();
        }

        function drawNode(node, color) {
            // Node Glow
            let activation = Math.min(node.val, 1.5); // Cap for visual
            let glowRadius = 10 + (activation * 20);
            
            let gradient = ctx.createRadialGradient(node.x, node.y, 8, node.x, node.y, glowRadius);
            gradient.addColorStop(0, color);
            gradient.addColorStop(1, 'rgba(0,0,0,0)');
            
            ctx.fillStyle = gradient;
            ctx.globalAlpha = 0.4 + (activation * 0.4);
            ctx.beginPath();
            ctx.arc(node.x, node.y, glowRadius, 0, Math.PI * 2);
            ctx.fill();
            ctx.globalAlpha = 1;

            // Node Body
            ctx.beginPath();
            ctx.arc(node.x, node.y, 12, 0, Math.PI * 2);
            ctx.fillStyle = '#222';
            ctx.strokeStyle = color;
            ctx.lineWidth = 2;
            ctx.fill();
            ctx.stroke();
        }

        function drawGraph() {
            const h = 80;
            const w = 200;
            const x = 20;
            const y = canvas.height - h - 20;

            // BG
            ctx.fillStyle = "rgba(0,0,0,0.5)";
            ctx.fillRect(x, y, w, h);
            ctx.strokeStyle = "#555";
            ctx.strokeRect(x, y, w, h);

            // Helper
            const drawLine = (data, color) => {
                ctx.beginPath();
                ctx.strokeStyle = color;
                ctx.lineWidth = 2;
                let step = w / maxGraphPoints;
                data.forEach((val, i) => {
                    let plotY = (y + h) - (Math.min(val, 1.5) / 1.5 * h);
                    if(i===0) ctx.moveTo(x + i*step, plotY);
                    else ctx.lineTo(x + i*step, plotY);
                });
                ctx.stroke();
            }

            drawLine(graphData.hiddenAvg, '#b388ff'); // Purple
            drawLine(graphData.output, '#8bc34a');   // Green
            
            ctx.font = "10px sans-serif";
            ctx.fillStyle = "#ccc";
            ctx.fillText("Live Activity", x+5, y-5);
        }

        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            updatePhysics();

            // 1. Draw Wires
            // Input -> Hidden
            layers.input.forEach(i => {
                layers.hidden.forEach(h => drawWire(i, h, 'excite'));
            });
            // Hidden -> Output
            layers.hidden.forEach(h => {
                layers.output.forEach(o => drawWire(h, o, 'excite'));
            });
            // Output -> Hidden (Feedback)
            layers.output.forEach(o => {
                layers.hidden.forEach(h => drawWire(o, h, 'inhibit'));
            });

            // 2. Draw Particles
            particles.forEach(p => {
                ctx.beginPath();
                ctx.fillStyle = p.type === 'inhibit' ? '#ff5252' : '#00bcd4';
                // Small size radius 2
                ctx.arc(p.x, p.y, 2, 0, Math.PI*2);
                ctx.fill();
            });

            // 3. Draw Nodes
            layers.input.forEach(n => drawNode(n, '#00bcd4')); // Cyan
            layers.hidden.forEach(n => drawNode(n, '#b388ff')); // Purple
            layers.output.forEach(n => drawNode(n, '#8bc34a')); // Green

            // 4. Draw Graph
            drawGraph();

            requestAnimationFrame(animate);
        }

        animate();

    </script>
</body>
</html>
