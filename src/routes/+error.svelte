<script lang="ts">
  import { page } from '$app/stores';
  import { Home, ArrowLeft, Trophy, AlertCircle, Sparkles } from 'lucide-svelte';
  import { goto } from '$app/navigation';
  
  let { status, error, message }: { status?: number; error?: Error; message?: string } = $props();
  
  // Get the error message
  const errorMessage = $derived(
    message || error?.message || 'The page you\'re looking for doesn\'t exist or has been moved.'
  );
  
  // Fixed particles configuration (doesn't change on re-render)
  const particles = Array(20).fill(null).map((_, i) => ({
    id: i,
    left: Math.random() * 100,
    top: Math.random() * 100,
    delay: Math.random() * 5,
    duration: 3 + Math.random() * 4,
    size: 2 + Math.random() * 3
  }));
</script>

<div class="min-h-screen bg-gradient-to-br from-gray-900 via-indigo-900 to-purple-900 flex items-center justify-center px-4 relative overflow-hidden">
  <!-- Animated background elements -->
  <div class="absolute inset-0 overflow-hidden pointer-events-none">
    <div class="absolute -top-40 -right-40 w-80 h-80 bg-purple-500 rounded-full blur-3xl opacity-20 animate-pulse"></div>
    <div class="absolute -bottom-40 -left-40 w-80 h-80 bg-indigo-500 rounded-full blur-3xl opacity-20 animate-pulse" style="animation-delay: 1s"></div>
    <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-96 h-96 bg-blue-500 rounded-full blur-3xl opacity-10"></div>
  </div>
  
  <!-- Fixed floating particles -->
  <div class="absolute inset-0 pointer-events-none">
    {#each particles as particle}
      <div 
        class="particle"
        style="
          left: {particle.left}%;
          top: {particle.top}%;
          width: {particle.size}px;
          height: {particle.size}px;
          animation-delay: {particle.delay}s;
          animation-duration: {particle.duration}s;
        "
      ></div>
    {/each}
  </div>
  
  <div class="relative max-w-2xl w-full text-center z-10">
    <!-- 404 Number -->
    <div class="mb-8 relative">
      <div class="text-[150px] md:text-[200px] font-black leading-none tracking-tighter">
        <span class="bg-gradient-to-r from-amber-400 via-orange-400 to-red-400 bg-clip-text text-transparent animate-gradient">
          4
        </span>
        <span class="bg-gradient-to-r from-blue-400 via-cyan-400 to-teal-400 bg-clip-text text-transparent animate-gradient" style="animation-delay: 0.1s">
          0
        </span>
        <span class="bg-gradient-to-r from-purple-400 via-pink-400 to-rose-400 bg-clip-text text-transparent animate-gradient" style="animation-delay: 0.2s">
          4
        </span>
      </div>
      <div class="absolute inset-0 flex items-center justify-center">
        <div class="w-32 h-32 md:w-40 md:h-40 bg-gradient-to-r from-amber-500/10 to-purple-500/10 rounded-full blur-2xl"></div>
      </div>
    </div>
    
    <!-- Error Icon -->
    <div class="inline-flex items-center justify-center w-20 h-20 bg-white/10 backdrop-blur-sm rounded-full mb-6 border border-white/20">
      <AlertCircle size={40} class="text-amber-400" />
    </div>
    
    <!-- Title -->
    <h1 class="text-3xl md:text-4xl font-bold text-white mb-4">
      Oops! Page Not Found
    </h1>
    
    <!-- Error Message -->
    <p class="text-gray-300 mb-8 max-w-md mx-auto">
      {errorMessage}
    </p>
    
    <!-- Status Code Badge -->
    <div class="inline-flex items-center gap-2 px-3 py-1 bg-white/10 backdrop-blur-sm rounded-full border border-white/20 mb-8">
      <span class="text-xs font-mono text-gray-300">Status Code: {status || 404}</span>
    </div>
    
    <!-- Action Buttons -->
    <div class="flex flex-col sm:flex-row gap-4 justify-center">
      <button
        onclick={() => goto('/')}
        class="inline-flex items-center justify-center gap-2 px-6 py-3 bg-gradient-to-r from-indigo-500 to-purple-600 hover:from-indigo-600 hover:to-purple-700 text-white font-semibold rounded-xl transition-all hover:shadow-lg hover:-translate-y-0.5"
      >
        <Home size={18} />
        Go Home
      </button>
      
      <button
        onclick={() => history.back()}
        class="inline-flex items-center justify-center gap-2 px-6 py-3 bg-white/10 backdrop-blur-sm hover:bg-white/20 text-white font-semibold rounded-xl transition-all border border-white/20"
      >
        <ArrowLeft size={18} />
        Go Back
      </button>
    </div>
    
    <!-- Helpful Links -->
    <div class="mt-12 pt-8 border-t border-white/10">
      <div class="flex items-center justify-center gap-2 text-sm text-gray-400 mb-4">
        <Sparkles size={14} />
        <span>You might be looking for:</span>
      </div>
      <div class="flex flex-wrap gap-3 justify-center">
        <a href="/" class="text-sm text-gray-300 hover:text-white transition-colors flex items-center gap-1">
          <Trophy size={14} />
          Prizes
        </a>
        <a href="/wallet/deposit" class="text-sm text-gray-300 hover:text-white transition-colors flex items-center gap-1">
          Deposit
        </a>
        <a href="/winners" class="text-sm text-gray-300 hover:text-white transition-colors flex items-center gap-1">
          Winners
        </a>
        <a href="/profile" class="text-sm text-gray-300 hover:text-white transition-colors flex items-center gap-1">
          Profile
        </a>
      </div>
    </div>
  </div>
</div>

<style>
  @keyframes float {
    0% {
      transform: translateY(0px) translateX(0px);
      opacity: 0;
    }
    25% {
      opacity: 0.5;
    }
    50% {
      transform: translateY(-20px) translateX(10px);
      opacity: 0.5;
    }
    75% {
      opacity: 0.3;
    }
    100% {
      transform: translateY(-40px) translateX(20px);
      opacity: 0;
    }
  }
  
  @keyframes gradient {
    0% {
      background-position: 0% 50%;
    }
    50% {
      background-position: 100% 50%;
    }
    100% {
      background-position: 0% 50%;
    }
  }
  
  @keyframes pulse {
    0%, 100% {
      opacity: 0.2;
      transform: scale(1);
    }
    50% {
      opacity: 0.3;
      transform: scale(1.05);
    }
  }
  
  .particle {
    position: absolute;
    background: rgba(255, 255, 255, 0.3);
    border-radius: 50%;
    pointer-events: none;
    animation: float linear infinite;
  }
  
  .animate-gradient {
    background-size: 200% auto;
    animation: gradient 3s ease infinite;
    display: inline-block;
  }
  
  .animate-pulse {
    animation: pulse 4s ease-in-out infinite;
  }
</style>