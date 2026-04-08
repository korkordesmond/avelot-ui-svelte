<script lang="ts">
  import { CircleUser, Bell, BanknoteArrowDown, BanknoteArrowUp, Trophy, Sparkles, TrendingUp, Shield } from 'lucide-svelte';
  import PrizeCard from '$lib/components/PrizeCard.svelte';

  const deals = [
    { name: 'This Week\'s Prize', amount: 96400, endsIn: new Date('2026-04-15') },
    { name: 'Monthly Mega Draw', amount: 1480000, endsIn: new Date('2026-04-10') },
    { name: 'Quarterly Jackpot', amount: 10800000, endsIn: new Date('2026-05-01') },
    { name: 'Summer Jackpot', amount: 34500000, endsIn: new Date('2026-08-03') },
    { name: 'Half-year Grand Prize', amount: 57600000, endsIn: new Date('2026-12-11') },
    { name: 'Annual Grand Prize', amount: 500000, endsIn: new Date('2026-12-12') },
  ];
</script>

<div class="min-h-screen bg-gradient-to-b from-slate-900 to-slate-800 pb-[6rem]">
  <!-- Header -->
  <div class="sticky top-0 z-10 backdrop-blur-lg bg-white/95 shadow-md">
    <div class="flex items-center justify-between px-4 py-3 max-w-7xl mx-auto">
      <a href="/profile" class="flex items-center space-x-3 group transition-opacity hover:opacity-80">
        <div class="relative">
          <CircleUser size={40} class="text-indigo-600" />
          <div class="absolute -bottom-1 -right-1 bg-emerald-500 rounded-full p-0.5">
            <Shield size={12} class="text-white" />
          </div>
        </div>
        <div class="flex flex-col">
          <span class="text-lg font-semibold text-slate-800">Jane</span>
          <span class="text-xs font-medium text-emerald-600">Verified Member</span>
        </div>
      </a>
      <a href="/notifications" class="relative p-2 rounded-full hover:bg-slate-100 transition-colors">
        <Bell size={24} class="text-slate-600" />
        <span class="absolute top-1 right-1 w-2 h-2 bg-rose-500 rounded-full"></span>
      </a>
    </div>
  </div>

  <!-- Balance Card -->
  <div class="relative mx-4 mt-6 mb-4">
    <div class="absolute inset-0 bg-gradient-to-r from-indigo-500 to-purple-600 rounded-2xl blur-xl opacity-30"></div>
    <div class="relative bg-gradient-to-br from-indigo-500 via-indigo-600 to-purple-700 rounded-2xl shadow-2xl overflow-hidden">
      <div class="absolute top-0 right-0 w-64 h-64 bg-white/5 rounded-full -mr-32 -mt-32"></div>
      <div class="absolute bottom-0 left-0 w-48 h-48 bg-white/5 rounded-full -ml-24 -mb-24"></div>
      <div class="relative px-6 py-8 text-center">
        <div class="inline-flex items-center gap-2 px-3 py-1 bg-white/10 rounded-full backdrop-blur-sm mb-4">
          <TrendingUp size={14} class="text-emerald-300" />
          <span class="text-xs font-medium text-white/80">Total Balance</span>
        </div>
        <div class="text-5xl font-bold text-white tracking-tight balance">
          $2,500,000
        </div>
        <div class="flex items-center justify-center gap-2 mt-3 text-white/60 text-sm">
          <span>≈ 2,000,000 pts</span>
          <span>•</span>
          <span>Available</span>
        </div>
      </div>
    </div>
  </div>

  <!-- Deposit and Withdraw Buttons -->
  <div class="px-4 py-6">
    <div class="flex gap-4 justify-center max-w-md mx-auto">
      <a href="/wallet/deposit" class="flex-1">
        <div class="group flex items-center justify-center gap-3 bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-600 hover:to-teal-700 rounded-xl py-3 px-4 transition-all duration-200 shadow-lg hover:shadow-xl hover:-translate-y-0.5">
          <BanknoteArrowUp size={22} class="text-white" />
          <span class="font-semibold text-white">Deposit</span>
        </div>
      </a>
      <a href="/wallet/withdraw" class="flex-1">
        <div class="group flex items-center justify-center gap-3 bg-gradient-to-r from-rose-500 to-pink-600 hover:from-rose-600 hover:to-pink-700 rounded-xl py-3 px-4 transition-all duration-200 shadow-lg hover:shadow-xl hover:-translate-y-0.5">
          <BanknoteArrowDown size={22} class="text-white" />
          <span class="font-semibold text-white">Withdraw</span>
        </div>
      </a>
    </div>
  </div>

  <!-- Prizes Section -->
  <div class="bg-white rounded-t-3xl shadow-2xl mt-2">
    <div class="px-5 pt-8 pb-2">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-3">
          <div class="p-2 bg-amber-100 rounded-xl">
            <Trophy size={28} class="text-amber-600" />
          </div>
          <div>
            <span class="text-2xl font-bold text-slate-800">Available Prizes</span>
            <p class="text-sm text-slate-500 mt-0.5">Join draws to win amazing rewards</p>
          </div>
        </div>
        <div class="flex items-center gap-1 text-xs font-medium text-amber-600 bg-amber-50 px-3 py-1.5 rounded-full">
          <Sparkles size={14} />
          <span>{deals.length} Active</span>
        </div>
      </div>
    </div>
    
    <div class="px-5 pb-8 pt-4">
      <div class="grid md:grid-cols-2 lg:grid-cols-3 gap-5">
        {#each deals as deal}
          <PrizeCard
            name={deal.name}
            amount={deal.amount}
            endsIn={deal.endsIn}
          />
        {/each}
      </div>
    </div>
  </div>
</div>

<style>
  .balance {
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    font-variant-numeric: tabular-nums;
    letter-spacing: -0.02em;
  }
</style>