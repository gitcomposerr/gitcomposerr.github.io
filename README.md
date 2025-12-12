<!DOCTYPE html>
<html lang="en">
<head>
    
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Childhood Impact</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Lato:wght@300;400;700&family=Playfair+Display:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-dark: #1a202c;
            --secondary-light: #f7fafc;
            --accent-gold: #d6bcfa;
        }
        body {
            font-family: 'Lato', sans-serif;
            overflow-x: hidden;
            background-color: var(--primary-dark);
            color: var(--secondary-light);
            margin: 0;
            padding: 0;
        }
        h1, h2, h3 {
            font-family: 'Playfair Display', serif;
        }
        
        /* Smooth Scrolling Container */
        .scroll-container {
            height: 100vh;
            overflow-y: scroll;
            scroll-snap-type: y mandatory;
            scroll-behavior: smooth;
        }

        .section {
            height: 100vh;
            width: 100%;
            scroll-snap-align: start;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            position: relative;
            padding: 2rem;
            box-sizing: border-box;
            opacity: 0;
            transform: translateY(50px) scale(0.95);
            transition: opacity 1.2s ease-out, transform 1.2s cubic-bezier(0.22, 1, 0.36, 1);
        }
        
        .section.visible {
            opacity: 1;
            transform: translateY(0) scale(1);
        }

        /* Animations */
        @keyframes fadeInUp {
            from {
                opacity: 0;
                transform: translateY(30px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .animate-fade-in-up {
            animation: fadeInUp 1.2s ease-out forwards;
        }

        .delay-100 { animation-delay: 0.2s; opacity: 0; animation-fill-mode: forwards; }
        .delay-300 { animation-delay: 0.6s; opacity: 0; animation-fill-mode: forwards; }
        .delay-500 { animation-delay: 1.0s; opacity: 0; animation-fill-mode: forwards; }

        /* Scroll Indicator */
        .scroll-indicator {
            position: absolute;
            bottom: 40px;
            left: 50%;
            transform: translateX(-50%);
            animation: bounce 2s infinite;
        }

        @keyframes bounce {
            0%, 20%, 50%, 80%, 100% {transform: translateX(-50%) translateY(0);}
            40% {transform: translateX(-50%) translateY(-10px);}
            60% {transform: translateX(-50%) translateY(-5px);}
        }
    </style>
</head>
<body>

    <div class="scroll-container">
        <!-- Page 1: Title -->
        <section class="section visible bg-gray-900 text-white" id="home">
            <div class="max-w-4xl text-center z-10">
                <h1 class="text-5xl md:text-7xl font-bold mb-6 tracking-tight animate-fade-in-up delay-100 leading-tight">
                    Childhood effect on <br/>
                    <span class="text-indigo-400 italic">decision making</span> <br/>
                    and behavior
                </h1>
                <p class="text-xl md:text-2xl text-gray-300 font-light max-w-2xl mx-auto animate-fade-in-up delay-300">
                    Made by <span class="text-indigo-400 font-normal">Victor Kanochkin</span>
                </p>
            </div>
            
            <!-- Background Decoration -->
            <div class="absolute inset-0 overflow-hidden pointer-events-none">
                <div class="absolute top-1/4 left-1/4 w-64 h-64 bg-indigo-600 rounded-full mix-blend-multiply filter blur-3xl opacity-20 animate-pulse"></div>
                <div class="absolute bottom-1/4 right-1/4 w-96 h-96 bg-purple-600 rounded-full mix-blend-multiply filter blur-3xl opacity-20 animate-pulse" style="animation-duration: 4s;"></div>
            </div>

            <div class="scroll-indicator animate-fade-in-up delay-500">
                <svg class="w-8 h-8 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 14l-7 7m0 0l-7-7m7 7V3"></path>
                </svg>
            </div>
        </section>

        <!-- Page 2: Homeostasis -->
        <section class="section bg-gray-900 text-white" id="homeostasis">
            <div class="max-w-6xl w-full mx-auto grid grid-cols-1 md:grid-cols-2 gap-12 items-center px-6">
                <!-- Left Side: Content -->
                <div class="text-left z-10">
                    <h2 class="text-3xl md:text-5xl font-bold mb-8 tracking-tight leading-tight text-indigo-100 font-serif">
                        What is homeostasis and how does it affect our behaviors?
                    </h2>
                    <p class="text-lg md:text-xl text-gray-300 font-light leading-relaxed border-l-4 border-indigo-500 pl-6">
                        Homeostasis is the self-regulating process by which biological organisms maintain stability while adjusting to conditions that are optimal for survival
                    </p>
                    
                    <div class="mt-8 bg-gray-800 bg-opacity-50 p-6 rounded-lg border-l-4 border-purple-500 backdrop-blur-sm transform transition-all hover:translate-x-2 duration-300 shadow-xl">
                        <p class="text-lg text-gray-300">
                            Organisms maintain homeostasis through <span class="text-indigo-300 font-semibold">positive</span> and <span class="text-indigo-300 font-semibold">negative</span> feedback loops.
                        </p>
                    </div>
                </div>
                
                <!-- Right Side: Open for now -->
                <div class="hidden md:block">
                    <!-- Reserved for future content -->
                </div>
            </div>
        </section>

        <!-- Page 3: Relevance -->
        <section class="section bg-gray-900 text-white" id="relevance">
            <div class="max-w-6xl w-full mx-auto px-6 z-10 flex flex-col items-center justify-center h-full">
                <h2 class="text-3xl md:text-5xl font-bold mb-12 tracking-tight leading-tight text-indigo-100 font-serif text-center max-w-4xl">
                    How is homeostasis relevant to childhood trauma and predispositions to mental disorders?
                </h2>
                <!-- Placeholder for content -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-8 w-full max-w-5xl">
                    <!-- Left and Right placeholders or content will go here -->
                </div>
            </div>
            
            <!-- Background Decoration -->
            <div class="absolute inset-0 overflow-hidden pointer-events-none">
                <div class="absolute top-1/3 right-1/4 w-80 h-80 bg-blue-900 rounded-full mix-blend-multiply filter blur-3xl opacity-20 animate-pulse"></div>
                <div class="absolute bottom-1/3 left-1/4 w-64 h-64 bg-purple-900 rounded-full mix-blend-multiply filter blur-3xl opacity-20 animate-pulse" style="animation-duration: 6s;"></div>
            </div>
        </section>
    </div>

    <script>
        // Intersection Observer for future sections
        document.addEventListener('DOMContentLoaded', () => {
            const sections = document.querySelectorAll('.section');
            
            const observer = new IntersectionObserver((entries) => {
                entries.forEach(entry => {
                    if (entry.isIntersecting) {
                        entry.target.classList.add('visible');
                    }
                });
            }, {
                threshold: 0.1
            });

            sections.forEach(section => {
                observer.observe(section);
            });
        });
    </script>
</body>
</html>
