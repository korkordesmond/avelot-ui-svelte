<script lang="ts">
  import { ArrowRight, Timer, Gift, Sparkles, Trophy } from 'lucide-svelte';

  interface Props {
    name: string;
    amount: number;
    endsIn: Date;
  }
  let { name, amount, endsIn }: Props = $props();

  const daysLeft = $derived(
    Math.max(0, Math.ceil((endsIn.getTime() - Date.now()) / (1000 * 60 * 60 * 24)))
  );
  const formattedAmount = $derived(
    new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD', maximumFractionDigits: 0 }).format(amount)
  );
  
  // Determine prize tier for styling
  const prizeTier = $derived(
    amount >= 10000000 ? 'jackpot' : amount >= 1000000 ? 'mega' : 'standard'
  );
  
  const tierColors = {
    jackpot: {
      border: 'border-amber-400',
      badge: 'from-amber-500 to-orange-500',
      button: 'from-amber-500 to-orange-500',
      amount: 'text-amber-400',
      tag: 'bg-amber-100 text-amber-700'
    },
    mega: {
      border: 'border-purple-400',
      badge: 'from-purple-500 to-pink-500',
      button: 'from-purple-500 to-pink-500',
      amount: 'text-purple-400',
      tag: 'bg-purple-100 text-purple-700'
    },
    standard: {
      border: 'border-blue-400',
      badge: 'from-blue-500 to-cyan-500',
      button: 'from-blue-500 to-cyan-500',
      amount: 'text-blue-400',
      tag: 'bg-blue-100 text-blue-700'
    }
  };
  
  const colors = tierColors[prizeTier];
</script>

<article class="group relative bg-white rounded-2xl border-2 {colors.border} overflow-hidden transition-all duration-300 hover:shadow-xl hover:-translate-y-1">
  <div class="p-5">
    <!-- Header -->
    <div class="flex justify-between items-start mb-4">
      <div class="flex items-center gap-2">
        <Trophy size={18} class="text-gray-500" />
        <span class="font-bold text-gray-800 text-base">{name}</span>
      </div>
      <div class="flex items-center gap-1.5 bg-gray-100 py-1.5 px-3 rounded-full">
        <Timer size={12} class="text-orange-500" />
        <span class="text-xs font-bold text-gray-700">
          {daysLeft} {daysLeft === 1 ? 'DAY' : 'DAYS'} LEFT
        </span>
      </div>
    </div>
    
    <!-- Prize Amount -->
    <div class="mb-5">
      <span class="text-4xl font-black {colors.amount}">
        {formattedAmount}
      </span>
      {#if prizeTier === 'jackpot'}
        <Sparkles size={20} class="text-amber-500 inline-block ml-2" />
      {/if}
    </div>
    
    <!-- Progress Bar -->
    <div class="mb-4">
      <div class="flex justify-between text-xs text-gray-500 mb-1">
        <span>Time remaining</span>
        <span>{daysLeft} days</span>
      </div>
      <div class="h-2 bg-gray-200 rounded-full overflow-hidden">
        <div class="h-full bg-gradient-to-r {colors.badge} rounded-full" style="width: {Math.min(100, (daysLeft / 30) * 100)}%"></div>
      </div>
    </div>
    
    <!-- Enter Button -->
    <a href="/draws/{name.toLowerCase().replace(/\s+/g, '-')}" class="block">
      <div class="flex items-center justify-between bg-gradient-to-r {colors.button} rounded-xl py-3 px-4 transition-all duration-300 hover:opacity-90 group/btn shadow-md">
        <span class="font-bold text-white text-sm tracking-wide">ENTER DRAW</span>
        <div class="bg-white/20 rounded-full p-1 group-hover/btn:translate-x-1 transition-transform">
          <ArrowRight size={14} class="text-white" />
        </div>
      </div>
    </a>
    
    <!-- Prize tier tag -->
    <div class="mt-3">
      {#if prizeTier === 'jackpot'}
        <span class="inline-flex items-center gap-1 text-xs font-semibold {colors.tag} px-2 py-1 rounded-full">
          <Sparkles size={10} />
          JACKPOT PRIZE
        </span>
      {:else if prizeTier === 'mega'}
        <span class="inline-flex items-center gap-1 text-xs font-semibold {colors.tag} px-2 py-1 rounded-full">
          <Gift size={10} />
          MEGA DRAW
        </span>
      {:else}
        <span class="inline-flex items-center gap-1 text-xs font-semibold text-gray-500 bg-gray-100 px-2 py-1 rounded-full">
          <Timer size={10} />
          STANDARD DRAW
        </span>
      {/if}
    </div>
  </div>
</article>