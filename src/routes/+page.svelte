<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">

<script>
    import { browser } from "$app/environment";
    import '$lib/sprite.class.js';
    import '../styles.css'
    import Navbar from "./Navbar.svelte";
</script>

<div class="fluid-container">
    <div class="element-above">
        <Navbar></Navbar>
    </div>

    <div class="element-below">
        <div id="canvas-container" class="row justify-content-center imageViewer-container">
            <canvas id="imageViewer"></canvas>
        </div>
    </div>
    
</div>

{#if browser}
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>

    <script>
        function initFabricCanvas() {
            // Get canvas element and container
            var canvasEl = document.getElementById('imageViewer');

            // Set canvas dimensions to match container dimensions
            canvasEl.width = window.screen.width;
            canvasEl.height = window.screen.height;

            //settings
            var zoomLevel = 0;
            var zoomLevelMin = 0;
            var zoomLevelMax = 3;

            var shiftKeyDown = false;
            var mouseDownPoint = null;

            var canvas = new fabric.Canvas('imageViewer');

            canvas.selection = false;

            fabric.Sprite.fromURL('./sourceImgPlaceholder.png', createSprite());

            function createSprite() {
                return function(sprite) {
                sprite.set({
                    left: 50,
                    top: 50,
                });
                sprite.set('selectable', false);
                canvas.add(sprite);
                setTimeout(function() {
                    sprite.set('dirty', true);
                    sprite.play();
                }, fabric.util.getRandomInt(1, 10) * 100);
                };
            }

            (function render() {
                canvas.renderAll();
                fabric.util.requestAnimFrame(render);
            })();
      
             // Set initial viewport transform for panning
            canvas.setViewportTransform([1, 0, 0, 1, 0, 0]);

            // Event listener for mouse wheel (zooming)
            canvas.on('mouse:wheel', function(opt) {
            var delta = opt.e.deltaY;
            var zoom = canvas.getZoom();
            zoom *= 0.999 ** delta;
            if (zoom > 20) zoom = 20; // Set maximum zoom level
            if (zoom < 0.01) zoom = 0.01; // Set minimum zoom level
            canvas.zoomToPoint({ x: opt.e.offsetX, y: opt.e.offsetY }, zoom);
            opt.e.preventDefault();
            opt.e.stopPropagation();
            });

            // Variables for panning
            let isPanning = false;
            let lastPosX = 0;
            let lastPosY = 0;

            // Event listener for mouse down (start panning)
            canvas.on('mouse:down', function(opt) {
            isPanning = true;
            var e = opt.e;
            lastPosX = e.clientX;
            lastPosY = e.clientY;
            });

            // Event listener for mouse move (panning)
            canvas.on('mouse:move', function(opt) {
            if (isPanning) {
                var e = opt.e;
                var vpt = canvas.viewportTransform;
                vpt[4] += e.clientX - lastPosX;
                vpt[5] += e.clientY - lastPosY;
                canvas.requestRenderAll();
                lastPosX = e.clientX;
                lastPosY = e.clientY;
            }
            });

            // Event listener for mouse up (stop panning)
            canvas.on('mouse:up', function(opt) {
            isPanning = false;
            });
         
            return canvas
        }

        //init the canvas
        var canvas = initFabricCanvas();

        //add listener to make the canvas resize when the browser resizes
        window.addEventListener('resize', function () {
            initFabricCanvas();
        });

        function openNav() {
        document.getElementById("mySidenav").style.width = "250px";
       
        }
        
        function closeNav() {
        document.getElementById("mySidenav").style.width = "0";
       
        }

    </script>

{/if}
