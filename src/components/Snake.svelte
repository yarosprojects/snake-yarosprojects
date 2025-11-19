<script lang="ts">
    import { onMount } from "svelte";
    import Score from "./Score.svelte";
    import Controls from "./Controls.svelte";

    const desktop_speed = 200;
    const mobile_speed = 450;
    const blockSize = 20;
    const spriteSize = 32;

    let canvas: HTMLCanvasElement;
    let ctx: CanvasRenderingContext2D | null = null;

    let foodImage = new Image();
    foodImage.src = '/food.png';
    let foodLoaded = false;

    let snakeImage = new Image();
    snakeImage.src = '/snake.png';
    let isImageLoaded = false;

    snakeImage.onload = () => { isImageLoaded = true; requestAnimationFrame(animate); startGameLoop(); };
    snakeImage.onerror = () => console.error("Could not load snake.png");

    foodImage.onload = () => { foodLoaded = true; };
    foodImage.onerror = () => console.error("Could not load food.png");
    let canvasWidth = 400;
    let canvasHeight = 400;
    let snake: { x: number; y: number }[] = [];
    let food: { x: number; y: number } = { x: 0, y: 0 };
    let direction: "up" | "down" | "left" | "right" = "right";
    let growing = false;
    let gameOver = false;
    let intervalId: number;
    let drawSnakePos: { x: number; y: number }[] = []; // SNAKE TRANSITION MOVMENT
    let score = 0; 

    function controlOnClick(dir: string) {
        if (
            (dir === "up" && direction !== "down") ||
            (dir === "down" && direction !== "up") ||
            (dir === "left" && direction !== "right") ||
            (dir === "right" && direction !== "left")
        ) {
            direction = dir;
        }
    }

    function resizeCanvas() {
        if (!canvas) return;
        const parent = canvas.parentElement;
        if (!parent) return;
        const size = Math.min(canvasWidth, canvasHeight || 400);
        canvasWidth = Math.floor(size / blockSize) * blockSize;
        canvasHeight = canvasWidth;
        canvas.width = canvasWidth;
        canvas.height = canvasHeight;
        drawField();
        drawSnake();
        drawFood();
    }

    function drawField() {
        if (!ctx) return;
        for (let y = 0; y < canvasHeight; y += blockSize) {
            for (let x = 0; x < canvasWidth; x += blockSize) {
                ctx.fillStyle =
                    (x / blockSize + y / blockSize) % 2 === 0
                        ? "#1a1a1a"
                        : "#222";
                ctx.fillRect(x, y, blockSize, blockSize);
            }
        }
        ctx.strokeStyle = "#2ecc71";
        ctx.lineWidth = 0.5;
    }

    function initSnake() {
        snake = [
            { x: 2, y: 2 },
            { x: 1, y: 2 },
            { x: 0, y: 2 },
        ];
        direction = "right";
    }

    function drawSnake() {
        if (!ctx) return;
        for (let i = 0; i < snake.length; i++) {
            if (!drawSnakePos[i]) drawSnakePos[i] = { ...snake[i] };

            drawSnakePos[i].x += (snake[i].x - drawSnakePos[i].x) * 0.2;
            drawSnakePos[i].y += (snake[i].y - drawSnakePos[i].y) * 0.2;

            if (snakeImage.complete) {
                ctx.drawImage(
                    snakeImage,
                    drawSnakePos[i].x * blockSize,
                    drawSnakePos[i].y * blockSize,
                    blockSize,
                    blockSize
                );
            } else {
                ctx.fillStyle = "#2ecc71";
                ctx.fillRect(
                    drawSnakePos[i].x * blockSize,
                    drawSnakePos[i].y * blockSize,
                    blockSize,
                    blockSize
                );
            }
        }
    }

    function spawnFood() {
        const cols = canvasWidth / blockSize;
        const rows = canvasHeight / blockSize;
        let x: number, y: number;
        do {
            x = Math.floor(Math.random() * cols);
            y = Math.floor(Math.random() * rows);
        } while (snake.some(segment => segment.x === x && segment.y === y));
        food.x = x;
        food.y = y;
    }

    function drawFood() {
        if (!ctx) return;

        if (foodLoaded) {
            ctx.drawImage(
                foodImage,
                food.x * blockSize,
                food.y * blockSize,
                blockSize,
                blockSize
            );
        } else {
            ctx.fillStyle = "#e74c3c";
            ctx.fillRect(
                food.x * blockSize,
                food.y * blockSize,
                blockSize,
                blockSize,
            );
        }
    }


    function moveSnake() {
        const head = { ...snake[0] };
        if (direction === "up") head.y -= 1;
        if (direction === "down") head.y += 1;
        if (direction === "left") head.x -= 1;
        if (direction === "right") head.x += 1;

        if (
            head.x < 0 ||
            head.y < 0 ||
            head.x >= canvasWidth / blockSize ||
            head.y >= canvasHeight / blockSize
        ) {
            gameOver = true;
            removeAnimateFadeIn();
            return;
        }

        if (
            snake.some(
                (segment) => segment.x === head.x && segment.y === head.y,
            )
        ) {
            gameOver = true;
            removeAnimateFadeIn();
            return;
        }

        snake.unshift(head);

        if (head.x === food.x && head.y === food.y) {
            growing = true;
            removeAnimateFadeIn();
            spawnFood();
            
            score += 1;
        }

        if (!growing) snake.pop();
        growing = false;
    }

    function removeAnimateFadeIn() {
        if (!canvas) return;
        if (canvas.classList.contains("animate-fade-in"))
            canvas.classList.remove("animate-fade-in");
    }

    function gameLoop() {
        moveSnake();
        drawField();
        drawFood();
        drawSnake();
    }

    function handleKey(e: KeyboardEvent) {
        if (
            ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].includes(e.key)
        ) {
            e.preventDefault(); // Evit page scrolling
        }

        if (e.key === "ArrowUp" && direction !== "down") direction = "up";
        if (e.key === "ArrowDown" && direction !== "up") direction = "down";
        if (e.key === "ArrowLeft" && direction !== "right") direction = "left";
        if (e.key === "ArrowRight" && direction !== "left") direction = "right";
    }

    onMount(() => {
        setTimeout(function () {
            if (canvas.classList.contains("animate-fade-in-up"))
                canvas.classList.remove("animate-fade-in-up");
        }, 1000);
        ctx = canvas.getContext("2d");
        resizeCanvas();
        initSnake();
        spawnFood();
        window.addEventListener("resize", () => {
            resizeCanvas();
            startGameLoop(); 
        });
        window.addEventListener("keydown", handleKey);
        startGameLoop();
        requestAnimationFrame(animate);
    });

    function startGameLoop() {
        const speed = window.innerWidth > 768 ? desktop_speed : mobile_speed;
        if(intervalId) clearInterval(intervalId);
        intervalId = setInterval(gameLoop, speed);
    }

    function getSpriteCoords(
        index: number,
        currentSnake: { x: number; y: number }[],
        currentDirection: "up" | "down" | "left" | "right"
    ): { sx: number; sy: number } {
        let sx = 0;
        let sy = 0;

        if (index === 0) {
            sx = 0 * spriteSize; sy = 0 * spriteSize; 
        } else {
            const prevSegment = currentSnake[index - 1];
            const nextSegment = currentSnake[index + 1];

            if (nextSegment) {
                if (prevSegment.x === nextSegment.x) {
                    sx = 2 * spriteSize; 
                    sy = 2 * spriteSize; 
                } else if (prevSegment.y === nextSegment.y) {
                    sx = 1 * spriteSize; 
                    sy = 2 * spriteSize; 
                } else {
                    sx = 1 * spriteSize; 
                    sy = 0 * spriteSize;
                }
            } else {
                 sx = 1 * spriteSize; 
                 sy = 2 * spriteSize;
            }
        }
        return { sx, sy };
    }

    function drawSnakeSmooth() {
        if (!ctx) return;

        for (let i = 0; i < snake.length; i++) {
            const segment = snake[i];
            if (!drawSnakePos[i]) drawSnakePos[i] = { ...segment };

            drawSnakePos[i].x += (snake[i].x - drawSnakePos[i].x) * 0.2;
            drawSnakePos[i].y += (snake[i].y - drawSnakePos[i].y) * 0.2;

            if (isImageLoaded) {
                const { sx, sy } = getSpriteCoords(i, snake, direction);

                ctx.drawImage(
                    snakeImage,
                    sx, 
                    sy, 
                    spriteSize,
                    spriteSize,
                    drawSnakePos[i].x * blockSize,
                    drawSnakePos[i].y * blockSize,
                    blockSize,
                    blockSize
                );
            } else {
                ctx.fillStyle = "#2ecc71";
                ctx.fillRect(
                    drawSnakePos[i].x * blockSize,
                    drawSnakePos[i].y * blockSize,
                    blockSize,
                    blockSize
                );
            }
        }
    }

    function animate() {
        drawField();
        drawFood();
        drawSnakeSmooth();
        requestAnimationFrame(animate);
    }


    function drawBlockStrokes(ctx: CanvasRenderingContext2D) {
        for (let x = 0; x <= canvasWidth; x += blockSize) {
            ctx.beginPath();
            ctx.moveTo(x, 0);
            ctx.lineTo(x, canvasHeight);
            ctx.stroke();
        }
        for (let y = 0; y <= canvasHeight; y += blockSize) {
            ctx.beginPath();
            ctx.moveTo(0, y);
            ctx.lineTo(canvasWidth, y);
            ctx.stroke();
        }
    }
</script>

{#if gameOver}
    <div class="fixed inset-0 flex items-center justify-center backdrop-blur-lg z-9998">
        <div class="bg-gray-900 rounded-3xl p-8 flex flex-col items-center shadow-lg animate-fade-in">
            <h1 class="text-white text-4xl font-bold mb-4">Game Over</h1>
            <button
                type="button"
                on:click={() => {
                    gameOver = false;
                    initSnake();
                    spawnFood();
                    score = 0;
                    if (!canvas.classList.contains("animate-fade-in")) canvas.classList.add("animate-fade-in");
                }}
                class="bg-yellow-500 text-black px-6 py-3 rounded-xl font-bold hover:bg-yellow-400 cursor-pointer active:scale-80 hover:scale-110 transition-all"
            >
                Play Again
            </button>
        </div>
    </div>
{/if}

<canvas
    bind:this={canvas}
    class="rounded-sm shadow-[0_0_20px_rgba(46,204,113,0.5)] max-w-full h-auto animate-fade-in-up"
></canvas>

<div class="flex w-full justify-center items-center mt-4 mb-8">
    <Score {score} />
</div>
<Controls {controlOnClick} />