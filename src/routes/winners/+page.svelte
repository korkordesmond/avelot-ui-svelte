<script lang="ts">
  import { Trophy, Users, Calendar, ArrowUpRight, Medal, Gift, TrendingUp, Sparkles, Crown } from 'lucide-svelte';

  // Sample winners data - replace with your actual data source
  interface Winner {
    id: number;
    name: string;
    amount: number;
    date: Date;
    prize: string;
    avatar?: string;
    tier: 'gold' | 'silver' | 'bronze';
  }

  const winners: Winner[] = [
    { id: 1, name: "Michael Chen", amount: 2500000, date: new Date('2026-03-15'), prize: "Quarterly Jackpot", tier: 'gold' },
    { id: 2, name: "Sarah Johnson", amount: 1480000, date: new Date('2026-03-10'), prize: "Monthly Mega Draw", tier: 'gold' },
    { id: 3, name: "David Williams", amount: 96400, date: new Date('2026-03-08'), prize: "This Week's Prize", tier: 'silver' },
    { id: 4, name: "Emma Rodriguez", amount: 500000, date: new Date('2026-03-05'), prize: "Annual Grand Prize", tier: 'silver' },
    { id: 5, name: "James Wilson", amount: 34500000, date: new Date('2026-02-28'), prize: "Summer Jackpot", tier: 'gold' },
    { id: 6, name: "Lisa Anderson", amount: 250000, date: new Date('2026-02-20'), prize: "Monthly Mega Draw", tier: 'bronze' },
    { id: 7, name: "Robert Taylor", amount: 10800000, date: new Date('2026-02-15'), prize: "Quarterly Jackpot", tier: 'gold' },
    { id: 8, name: "Maria Garcia", amount: 75000, date: new Date('2026-02-10'), prize: "This Week's Prize", tier: 'bronze' },
    { id: 9, name: "Thomas Brown", amount: 57600000, date: new Date('2026-02-05'), prize: "Half-year Grand Prize", tier: 'gold' },
    { id: 10, name: "Jennifer Lee", amount: 320000, date: new Date('2026-01-28'), prize: "Monthly Mega Draw", tier: 'silver' },
  ];

  // Calculate total payout
  const totalPayout = $derived(
    winners.reduce((sum, winner) => sum + winner.amount, 0)
  );

  const formattedTotalPayout = $derived(
    new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD', maximumFractionDigits: 0 }).format(totalPayout)
  );

  // Get recent winners (last 30 days)
  const recentWinners = $derived(
    winners.filter(w => w.date > new Date(Date.now() - 30 * 24 * 60 * 60 * 1000))
  );

  // Group winners by tier
  const goldWinners = winners.filter(w => w.tier === 'gold');
  const silverWinners = winners.filter(w => w.tier === 'silver');
  const bronzeWinners = winners.filter(w => w.tier === 'bronze');

  // Format date
  function formatDate(date: Date): string {
    return new Intl.DateTimeFormat('en-US', { month: 'short', day: 'numeric', year: 'numeric' }).format(date);
  }

  // Format amount
  function formatAmount(amount: number): string {
    if (amount >= 1000000) {
      return `$${(amount / 1000000).toFixed(1)}M`;
    }
    if (amount >= 1000) {
      return `$${(amount / 1000).toFixed(0)}K`;
    }
    return `$${amount.toLocaleString()}`;
  }

  // Get tier icon and color
  function getTierInfo(tier: string) {
    switch(tier) {
      case 'gold':
        return { icon: Crown, color: 'text-amber-500', bg: 'bg-amber-100', border: 'border-amber-200' };
      case 'silver':
        return { icon: Medal, color: 'text-gray-500', bg: 'bg-gray-100', border: 'border-gray-200' };
      case 'bronze':
        return { icon: Gift, color: 'text-orange-600', bg: 'bg-orange-100', border: 'border-orange-200' };
      default:
        return { icon: Trophy, color: 'text-blue-500', bg: 'bg-blue-100', border: 'border-blue-200' };
    }
  }
</script>

<div class="min-h-screen bg-gradient-to-br from-gray-50 to-gray-100 pb-[6rem]">
  <!-- Header -->
  <div class="bg-white border-b border-gray-200 sticky top-0 z-10 shadow-sm">
    <div class="max-w-7xl mx-auto px-4 py-4">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-3">
          <div class="p-2 bg-gradient-to-r from-amber-500 to-orange-500 rounded-xl">
            <Trophy size={24} class="text-white" />
          </div>
          <div>
            <h1 class="text-2xl font-bold text-gray-900">Winners Circle</h1>
            <p class="text-sm text-gray-500">Celebrating our lucky winners</p>
          </div>
        </div>
        <div class="flex items-center gap-2 text-sm text-gray-500">
          <Users size={16} />
          <span>{winners.length} Winners</span>
        </div>
      </div>
    </div>
  </div>

  <!-- Total Payout Card -->
  <div class="max-w-7xl mx-auto px-4 py-6">
    <div class="relative bg-gradient-to-br from-indigo-600 via-purple-600 to-pink-600 rounded-2xl shadow-2xl overflow-hidden">
      <div class="absolute top-0 right-0 w-64 h-64 bg-white/10 rounded-full -mr-32 -mt-32"></div>
      <div class="absolute bottom-0 left-0 w-48 h-48 bg-white/10 rounded-full -ml-24 -mb-24"></div>
      <div class="relative px-6 py-8 text-center">
        <div class="inline-flex items-center gap-2 px-3 py-1 bg-white/20 rounded-full backdrop-blur-sm mb-4">
          <TrendingUp size={14} class="text-emerald-300" />
          <span class="text-xs font-medium text-white">Total Payout to Date</span>
        </div>
        <div class="text-5xl md:text-6xl font-bold text-white mb-3">
          {formattedTotalPayout}
        </div>
        <div class="flex items-center justify-center gap-4 text-white/80 text-sm">
          <span class="flex items-center gap-1">
            <Gift size={14} />
            {winners.length} Winners
          </span>
          <span>•</span>
          <span class="flex items-center gap-1">
            <Calendar size={14} />
            Lifetime
          </span>
        </div>
      </div>
    </div>
  </div>

  <!-- Stats Grid -->
  <div class="max-w-7xl mx-auto px-4 pb-6">
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
      <div class="bg-white rounded-xl p-4 shadow-sm border border-gray-200">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-sm text-gray-500">Gold Tier Winners</p>
            <p class="text-2xl font-bold text-amber-600">{goldWinners.length}</p>
          </div>
          <div class="p-3 bg-amber-100 rounded-full">
            <Crown size={24} class="text-amber-600" />
          </div>
        </div>
        <p class="text-xs text-gray-400 mt-2">Biggest prize winners</p>
      </div>
      <div class="bg-white rounded-xl p-4 shadow-sm border border-gray-200">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-sm text-gray-500">Silver Tier Winners</p>
            <p class="text-2xl font-bold text-gray-600">{silverWinners.length}</p>
          </div>
          <div class="p-3 bg-gray-100 rounded-full">
            <Medal size={24} class="text-gray-600" />
          </div>
        </div>
        <p class="text-xs text-gray-400 mt-2">Consistent winners</p>
      </div>
      <div class="bg-white rounded-xl p-4 shadow-sm border border-gray-200">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-sm text-gray-500">Bronze Tier Winners</p>
            <p class="text-2xl font-bold text-orange-600">{bronzeWinners.length}</p>
          </div>
          <div class="p-3 bg-orange-100 rounded-full">
            <Gift size={24} class="text-orange-600" />
          </div>
        </div>
        <p class="text-xs text-gray-400 mt-2">New & rising stars</p>
      </div>
    </div>
  </div>

  <!-- Recent Winners Section -->
  {#if recentWinners.length > 0}
    <div class="max-w-7xl mx-auto px-4 pb-6">
      <div class="flex items-center gap-2 mb-4">
        <Sparkles size={20} class="text-amber-500" />
        <h2 class="text-xl font-bold text-gray-900">Recent Winners</h2>
        <span class="text-sm text-gray-500">Last 30 days</span>
      </div>
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        {#each recentWinners as winner}
          {@const tierInfo = getTierInfo(winner.tier)}
          <div class="bg-white rounded-xl p-4 shadow-sm border border-gray-200 hover:shadow-md transition-shadow">
            <div class="flex items-center gap-3 mb-3">
              <div class="w-10 h-10 {tierInfo.bg} rounded-full flex items-center justify-center">
                <tierInfo.icon size={20} class={tierInfo.color} />
              </div>
              <div class="flex-1">
                <h3 class="font-semibold text-gray-900">{winner.name}</h3>
                <p class="text-xs text-gray-500">{winner.prize}</p>
              </div>
              <ArrowUpRight size={16} class="text-gray-400" />
            </div>
            <div class="flex justify-between items-center">
              <div>
                <p class="text-2xl font-bold text-gray-900">${winner.amount.toLocaleString()}</p>
                <p class="text-xs text-gray-400">{formatDate(winner.date)}</p>
              </div>
              <div class="text-right">
                <span class="inline-block px-2 py-1 {tierInfo.bg} {tierInfo.color} text-xs font-semibold rounded-full capitalize">
                  {winner.tier}
                </span>
              </div>
            </div>
          </div>
        {/each}
      </div>
    </div>
  {/if}

  <!-- All Winners Table -->
  <div class="max-w-7xl mx-auto px-4 pb-12">
    <div class="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
      <div class="px-6 py-4 border-b border-gray-200 bg-gray-50">
        <h2 class="font-semibold text-gray-900">All Time Winners</h2>
      </div>
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-50 border-b border-gray-200">
            <tr>
              <th class="text-left px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Winner</th>
              <th class="text-left px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Prize</th>
              <th class="text-left px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Amount Won</th>
              <th class="text-left px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Date</th>
              <th class="text-left px-6 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">Tier</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            {#each winners as winner}
              {@const tierInfo = getTierInfo(winner.tier)}
              <tr class="hover:bg-gray-50 transition-colors">
                <td class="px-6 py-4">
                  <div class="flex items-center gap-3">
                    <div class="w-8 h-8 {tierInfo.bg} rounded-full flex items-center justify-center">
                      <tierInfo.icon size={16} class={tierInfo.color} />
                    </div>
                    <span class="font-medium text-gray-900">{winner.name}</span>
                  </div>
                </td>
                <td class="px-6 py-4 text-gray-600">{winner.prize}</td>
                <td class="px-6 py-4">
                  <span class="font-bold text-gray-900">${winner.amount.toLocaleString()}</span>
                </td>
                <td class="px-6 py-4 text-gray-500 text-sm">{formatDate(winner.date)}</td>
                <td class="px-6 py-4">
                  <span class="inline-flex items-center gap-1 px-2 py-1 {tierInfo.bg} {tierInfo.color} text-xs font-semibold rounded-full capitalize">
                    <tierInfo.icon size={12} />
                    {winner.tier}
                  </span>
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>