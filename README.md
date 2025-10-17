<!DOCTYPE html>
<html>
<head>
    <title>Playful BRI AR Globe</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- A-Frame and AR.js libraries -->
    <script src="https://aframe.io/releases/1.3.0/aframe.min.js"></script>
    <script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar.js"></script>
    <style>
        body { margin: 0; overflow: hidden; }
        .instructions {
            position: fixed;
            bottom: 10px;
            left: 50%;
            transform: translateX(-50%);
            width: 90%;
            max-width: 400px;
            background-color: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 10px;
            border-radius: 8px;
            text-align: center;
            font-family: sans-serif;
            font-size: 14px;
            z-index: 100;
        }
    </style>
</head>
<body style="margin: 0px; overflow: hidden;">
    <!-- AR Scene -->
    <a-scene embedded arjs="sourceType: webcam; debugUIEnabled: false; trackingMethod: best;">

        <!-- The marker that the camera will detect -->
        <a-marker preset="hiro">

            <!-- The main Globe entity, which will spin -->
            <a-entity id="globe" position="0 0.8 0">
                <a-animation attribute="rotation" dur="45000" to="0 360 0" repeat="indefinite" easing="linear"></a-animation>
                
                <!-- Globe Sphere with a simple map texture -->
                <a-sphere radius="0.6" material="src: url(https://raw.githubusercontent.com/jeromeetienne/threex.planets/master/images/earthmoonspecular.jpg); color: #6db5ff;">
                </a-sphere>
            </a-entity>

            <!-- Container for the animated transport routes -->
            <a-entity id="transport" position="0 0.8 0">
                <!-- 1. The Train (Overland Route) -->
                <a-entity id="train-orbit" rotation="0 0 -25">
                    <a-animation attribute="rotation" dur="15000" to="0 360 -25" repeat="indefinite" easing="linear"></a-animation>
                    <a-entity id="train" position="0.75 0 0">
                        <a-box position="0 0 0" depth="0.08" height="0.04" width="0.04" color="#d9534f"></a-box>
                        <a-box position="-0.06 0 0" depth="0.06" height="0.03" width="0.03" color="#f0ad4e"></a-box>
                        <a-box position="-0.12 0 0" depth="0.06" height="0.03" width="0.03" color="#5cb85c"></a-box>
                    </a-entity>
                </a-entity>
                
                <!-- 2. The Ship (Maritime Route) -->
                <a-entity id="ship-orbit" rotation="15 0 0">
                    <a-animation attribute="rotation" dur="20000" to="15 360 0" repeat="indefinite" easing="linear"></a-animation>
                    <a-entity id="ship" position="0 0 0.8">
                         <a-box position="0 -0.01 0" depth="0.1" height="0.04" width="0.05" color="#5bc0de"></a-box>
                         <a-box position="0 0.03 0" depth="0.04" height="0.04" width="0.03" color="#ffffff"></a-box>
                    </a-entity>
                </a-entity>

                <!-- 3. The Plane (Air Route) -->
                 <a-entity id="plane-orbit" rotation="-20 0 30">
                    <a-animation attribute="rotation" dur="10000" to="-20 360 30" repeat="indefinite" easing="linear"></a-animation>
                    <a-entity id="plane" position="0 0.9 0" scale="0.8 0.8 0.8">
                        <a-cylinder position="0 0 0" radius="0.02" height="0.15" rotation="90 0 0" color="#f7f7f7"></a-cylinder>
                        <a-box position="0 0 0" depth="0.01" height="0.02" width="0.12" color="#cfcfcf"></a-box>
                    </a-entity>
                </a-entity>
            </a-entity>
        </a-marker>

        <a-entity camera></a-entity>
    </a-scene>
    
    <div class="instructions">
        Point camera at the Hiro marker to see the playful AR Globe!
        
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function () {
            // Data for investment bars: [lat, lon, investment_scale(1-10), color]
            const investmentData = [
                { name: 'Pakistan', coords: [30, 69], scale: 10, color: '#e63946' },
                { name: 'Kenya', coords: [-1, 36], scale: 6, color: '#fca311' },
                { name: 'Laos', coords: [19, 102], scale: 7, color: '#a8dadc' },
                { name: 'Indonesia', coords: [-6, 107], scale: 8, color: '#457b9d' },
                { name: 'Greece', coords: [38, 23], scale: 5, color: '#1d3557' },
                { name: 'Kazakhstan', coords: [48, 66], scale: 9, color: '#ffc300' }
            ];

            const globe = document.getElementById('globe');
            const radius = 0.6; // Globe radius

            investmentData.forEach(data => {
                const [lat, lon, scale, color] = [data.coords[0], data.coords[1], data.scale, data.color];
                
                // Create a container to handle rotations easily
                const container = document.createElement('a-entity');
                container.setAttribute('rotation', `0 ${lon} 0`);
                
                const innerContainer = document.createElement('a-entity');
                innerContainer.setAttribute('rotation', `${-lat} 0 0`);
                
                // Create the investment bar (cylinder)
                const bar = document.createElement('a-cylinder');
                const barHeight = scale * 0.03;
                bar.setAttribute('position', `0 ${radius + barHeight / 2} 0`);
                bar.setAttribute('radius', '0.015');
                bar.setAttribute('height', barHeight);
                bar.setAttribute('color', color);
                
                // Add a playful bobbing animation
                bar.innerHTML = `<a-animation attribute="position" dur="1500" direction="alternate" from="0 ${radius + barHeight/2} 0" to="0 ${radius + barHeight/2 + 0.05} 0" repeat="indefinite" easing="ease-in-out-sine"></a-animation>`;

                // Create a text label for the country
                const label = document.createElement('a-text');
                label.setAttribute('value', data.name);
                label.setAttribute('position', `0 ${radius + barHeight + 0.08} 0`);
                label.setAttribute('rotation', '0 -90 0');
                label.setAttribute('align', 'center');
                label.setAttribute('width', '1.5');
                label.setAttribute('color', '#ffffff');
                 // Use a built-in A-Frame font
                label.setAttribute('font', 'sourcecodepro');


                innerContainer.appendChild(bar);
                innerContainer.appendChild(label);
                container.appendChild(innerContainer);
                globe.appendChild(container);
            });
        });
    </script>
</body>
</html>
# simonpbernhard
