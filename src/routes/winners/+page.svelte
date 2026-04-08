<script lang="ts">
  import { Trophy, Users, Calendar, ArrowUpRight, Medal, Gift, TrendingUp, Sparkles, Crown, ChevronRight, Award, Clock, Star, Zap } from 'lucide-svelte';

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

  // Top 3 winners
  const topWinners = [...winners].sort((a, b) => b.amount - a.amount).slice(0, 3);

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

  // Get tier info
  function getTierInfo(tier: string) {
    switch(tier) {
      case 'gold':
        return { icon: Crown, color: 'text-amber-600', bg: 'bg-amber-50', border: 'border-amber-100', label: 'Gold' };
      case 'silver':
        return { icon: Medal, color: 'text-gray-500', bg: 'bg-gray-50', border: 'border-gray-100', label: 'Silver' };
      case 'bronze':
        return { icon: Gift, color: 'text-orange-600', bg: 'bg-orange-50', border: 'border-orange-100', label: 'Bronze' };
      default:
        return { icon: Trophy, color: 'text-blue-600', bg: 'bg-blue-50', border: 'border-blue-100', label: 'Winner' };
    }
  }
</script>

<div class="page">
  <!-- Header -->
  <header class="topbar">
    <div class="topbar-inner">
      <div class="logo-area">
        <div class="icon-wrap">
          <Trophy size={22} />
        </div>
        <div>
          <h1>Winners Circle</h1>
          <p>Celebrating our lucky winners</p>
        </div>
      </div>
      <div class="stats-badge">
        <Users size={16} />
        <span>{winners.length} Winners</span>
      </div>
    </div>
  </header>

  <main class="content">
    <!-- Total Payout Card -->
    <div class="payout-card">
      <div class="payout-label">
        <TrendingUp size={13} />
        <span>Total Payout to Date</span>
      </div>
      <div class="payout-amount">{formattedTotalPayout}</div>
      <div class="payout-sub">
        <span>≈ {totalPayout / 1000000}M pts</span>
        <span class="dot">•</span>
        <span>Lifetime</span>
      </div>
    </div>

    <!-- Top Winners Podium -->
    <div class="podium-section">
      <div class="section-header">
        <div class="section-title">
          <Crown size={18} class="title-icon" />
          <div>
            <h2>Top Winners</h2>
            <p>Highest earners all time</p>
          </div>
        </div>
        <span class="section-badge">All Time</span>
      </div>
      <div class="podium-grid">
        {#each topWinners as winner, idx}
          <div class="podium-card {idx === 0 ? 'first' : idx === 1 ? 'second' : 'third'}">
            <div class="podium-rank">{idx + 1}</div>
            <div class="podium-avatar">
              <span class="avatar-initial">{winner.name.charAt(0)}</span>
            </div>
            <div class="podium-name">{winner.name.split(' ')[0]}</div>
            <div class="podium-amount">{formatAmount(winner.amount)}</div>
            <div class="podium-prize">{winner.prize}</div>
          </div>
        {/each}
      </div>
    </div>

    <!-- Stats Row -->
    <div class="stats-row">
      <div class="stat-card">
        <div class="stat-icon gold-bg">
          <Crown size={18} />
        </div>
        <div class="stat-info">
          <span class="stat-value">{goldWinners.length}</span>
          <span class="stat-label">Gold Winners</span>
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon silver-bg">
          <Medal size={18} />
        </div>
        <div class="stat-info">
          <span class="stat-value">{silverWinners.length}</span>
          <span class="stat-label">Silver Winners</span>
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon bronze-bg">
          <Gift size={18} />
        </div>
        <div class="stat-info">
          <span class="stat-value">{bronzeWinners.length}</span>
          <span class="stat-label">Bronze Winners</span>
        </div>
      </div>
    </div>

    <!-- Recent Winners -->
    {#if recentWinners.length > 0}
      <section class="prizes-section">
        <div class="prizes-header">
          <div class="prizes-title-group">
            <Sparkles size={20} class="sparkle-icon" />
            <div>
              <h2 class="prizes-title">Recent Winners</h2>
              <p class="prizes-sub">Last 30 days</p>
            </div>
          </div>
          <span class="active-badge">{recentWinners.length} New</span>
        </div>
        <div class="prizes-grid">
          {#each recentWinners as winner}
            {@const tierInfo = getTierInfo(winner.tier)}
            <div class="prize-card">
              <div class="prize-card-inner">
                <div class="prize-top">
                  <div class="winner-name-group">
                    <div class="winner-avatar {tierInfo.bg}">
                      <tierInfo.icon size={14} class={tierInfo.color} />
                    </div>
                    <span class="prize-name">{winner.name}</span>
                  </div>
                  <span class="prize-badge">{winner.prize}</span>
                </div>
                <div class="prize-amount">{formatAmount(winner.amount)}</div>
                <div class="prize-bottom">
                  <div class="prize-time">
                    <Clock size={12} />
                    <span>{formatDate(winner.date)}</span>
                  </div>
                  <span class="tier-chip {winner.tier}">{tierInfo.label}</span>
                </div>
              </div>
            </div>
          {/each}
        </div>
      </section>
    {/if}

    <!-- All Winners Table -->
    <section class="prizes-section">
      <div class="prizes-header">
        <div class="prizes-title-group">
          <Award size={20} class="award-icon" />
          <div>
            <h2 class="prizes-title">All Winners</h2>
            <p class="prizes-sub">Complete history</p>
          </div>
        </div>
        <span class="active-badge">{winners.length} Total</span>
      </div>
      <div class="winners-table">
        <div class="table-header">
          <span>Winner</span>
          <span>Prize</span>
          <span>Amount</span>
          <span>Date</span>
          <span>Tier</span>
        </div>
        <div class="table-body">
          {#each winners as winner}
            {@const tierInfo = getTierInfo(winner.tier)}
            <div class="table-row">
              <div class="winner-cell">
                <div class="winner-avatar-sm {tierInfo.bg}">
                  <tierInfo.icon size={12} class={tierInfo.color} />
                </div>
                <span>{winner.name}</span>
              </div>
              <span class="prize-cell">{winner.prize}</span>
              <span class="amount-cell">${winner.amount.toLocaleString()}</span>
              <span class="date-cell">{formatDate(winner.date)}</span>
              <span class="tier-cell {winner.tier}">{tierInfo.label}</span>
            </div>
          {/each}
        </div>
      </div>
    </section>
  </main>
</div>

<style>
  .page {
    min-height: 100vh;
    background: #f5f2eb;
    padding-bottom: 4rem;
    font-family: 'DM Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  }

  /* Header */
  .topbar {
    position: sticky;
    top: 0;
    z-index: 10;
    background: #faf8f4;
    border-bottom: 1px solid #e8e4dc;
  }

  .topbar-inner {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 20px;
    max-width: 1100px;
    margin: 0 auto;
  }

  .logo-area {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .icon-wrap {
    width: 44px;
    height: 44px;
    background: linear-gradient(135deg, #f59e0b, #ea580c);
    border-radius: 14px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
  }

  .logo-area h1 {
    font-size: 18px;
    font-weight: 600;
    color: #2c2418;
    margin: 0;
    line-height: 1.2;
  }

  .logo-area p {
    font-size: 11px;
    color: #9b8f7e;
    margin: 0;
  }

  .stats-badge {
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 6px 12px;
    background: #f0ebe3;
    border-radius: 40px;
    font-size: 12px;
    font-weight: 500;
    color: #4a3e2e;
  }

  /* Content */
  .content {
    max-width: 1100px;
    margin: 0 auto;
    padding: 24px 20px 0;
  }

  /* Payout Card */
  .payout-card {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 20px;
    padding: 28px 24px;
    text-align: center;
    margin-bottom: 24px;
  }

  .payout-label {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    font-size: 11px;
    font-weight: 500;
    color: #9b8f7e;
    background: #f0ebe3;
    border-radius: 40px;
    padding: 4px 12px;
    margin-bottom: 12px;
  }

  .payout-amount {
    font-size: 48px;
    font-weight: 600;
    color: #2c2418;
    letter-spacing: -0.02em;
    line-height: 1;
    margin-bottom: 8px;
  }

  .payout-sub {
    font-size: 12px;
    color: #b8ab9a;
  }

  .dot {
    margin: 0 6px;
  }

  /* Podium Section */
  .podium-section {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 20px;
    padding: 20px;
    margin-bottom: 20px;
  }

  .section-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 20px;
    flex-wrap: wrap;
    gap: 12px;
  }

  .section-title {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .title-icon {
    color: #b8ab9a;
  }

  .section-title h2 {
    font-size: 16px;
    font-weight: 600;
    color: #2c2418;
    margin: 0 0 2px;
    line-height: 1;
  }

  .section-title p {
    font-size: 11px;
    color: #b8ab9a;
    margin: 0;
  }

  .section-badge {
    font-size: 11px;
    font-weight: 500;
    color: #9b8f7e;
    background: #f0ebe3;
    border-radius: 40px;
    padding: 4px 12px;
  }

  .podium-grid {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  @media (min-width: 640px) {
    .podium-grid {
      flex-direction: row;
      gap: 16px;
    }
  }

  .podium-card {
    flex: 1;
    text-align: center;
    padding: 16px;
    background: #fefdfb;
    border: 1px solid #e8e4dc;
    border-radius: 16px;
    transition: all 0.2s;
  }

  .podium-card.first {
    background: linear-gradient(135deg, #fefcf8, #fffbeb);
    border-color: #fde68a;
  }

  .podium-card.second {
    background: linear-gradient(135deg, #fefcf8, #f9fafb);
    border-color: #e5e7eb;
  }

  .podium-card.third {
    background: linear-gradient(135deg, #fefcf8, #fff7ed);
    border-color: #fed7aa;
  }

  .podium-rank {
    font-size: 14px;
    font-weight: 600;
    color: #b8ab9a;
    margin-bottom: 8px;
  }

  .podium-card.first .podium-rank { color: #f59e0b; }
  .podium-card.second .podium-rank { color: #6b7280; }
  .podium-card.third .podium-rank { color: #ea580c; }

  .podium-avatar {
    width: 56px;
    height: 56px;
    margin: 0 auto 10px;
    background: #f0ebe3;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .podium-card.first .podium-avatar { background: linear-gradient(135deg, #fbbf24, #f59e0b); }
  .podium-card.second .podium-avatar { background: linear-gradient(135deg, #d1d5db, #9ca3af); }
  .podium-card.third .podium-avatar { background: linear-gradient(135deg, #fb923c, #ea580c); }

  .avatar-initial {
    font-size: 20px;
    font-weight: 600;
    color: white;
  }

  .podium-name {
    font-weight: 600;
    color: #2c2418;
    margin-bottom: 4px;
  }

  .podium-amount {
    font-size: 18px;
    font-weight: 700;
    color: #2c2418;
    margin-bottom: 4px;
  }

  .podium-prize {
    font-size: 10px;
    color: #b8ab9a;
  }

  /* Stats Row */
  .stats-row {
    display: grid;
    grid-template-columns: 1fr;
    gap: 12px;
    margin-bottom: 24px;
  }

  @media (min-width: 640px) {
    .stats-row {
      grid-template-columns: repeat(3, 1fr);
    }
  }

  .stat-card {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 16px;
    padding: 14px 16px;
    display: flex;
    align-items: center;
    gap: 14px;
  }

  .stat-icon {
    width: 44px;
    height: 44px;
    border-radius: 14px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .gold-bg { background: #fef3c7; color: #f59e0b; }
  .silver-bg { background: #f3f4f6; color: #6b7280; }
  .bronze-bg { background: #ffedd5; color: #ea580c; }

  .stat-info {
    display: flex;
    flex-direction: column;
  }

  .stat-value {
    font-size: 22px;
    font-weight: 600;
    color: #2c2418;
    line-height: 1;
  }

  .stat-label {
    font-size: 11px;
    color: #b8ab9a;
    font-weight: 500;
  }

  /* Prizes Section */
  .prizes-section {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 20px;
    padding: 20px;
    margin-bottom: 20px;
  }

  .prizes-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 20px;
    flex-wrap: wrap;
    gap: 12px;
  }

  .prizes-title-group {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .sparkle-icon, .award-icon {
    color: #b8ab9a;
  }

  .prizes-title {
    font-size: 16px;
    font-weight: 600;
    color: #2c2418;
    margin: 0 0 2px;
    line-height: 1;
  }

  .prizes-sub {
    font-size: 11px;
    color: #b8ab9a;
    margin: 0;
  }

  .active-badge {
    font-size: 11px;
    font-weight: 500;
    color: #9b8f7e;
    background: #f0ebe3;
    border-radius: 40px;
    padding: 4px 12px;
  }

  .prizes-grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 12px;
  }

  @media (min-width: 640px) {
    .prizes-grid {
      grid-template-columns: repeat(2, 1fr);
    }
  }

  @media (min-width: 900px) {
    .prizes-grid {
      grid-template-columns: repeat(3, 1fr);
    }
  }

  .prize-card {
    background: #fefdfb;
    border: 1px solid #e8e4dc;
    border-radius: 16px;
    transition: all 0.2s;
  }

  .prize-card:hover {
    border-color: #ddd6cc;
    background: #ffffff;
  }

  .prize-card-inner {
    padding: 14px;
  }

  .prize-top {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
    flex-wrap: wrap;
    gap: 8px;
  }

  .winner-name-group {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .winner-avatar {
    width: 28px;
    height: 28px;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .prize-name {
    font-size: 14px;
    font-weight: 500;
    color: #2c2418;
  }

  .prize-badge {
    font-size: 9px;
    font-weight: 500;
    color: #9b8f7e;
    background: #f0ebe3;
    padding: 3px 8px;
    border-radius: 20px;
  }

  .prize-amount {
    font-size: 20px;
    font-weight: 700;
    color: #2c2418;
    margin-bottom: 10px;
  }

  .prize-bottom {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding-top: 8px;
    border-top: 1px solid #e8e4dc;
  }

  .prize-time {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 10px;
    color: #b8ab9a;
  }

  .tier-chip {
    font-size: 9px;
    font-weight: 600;
    padding: 3px 8px;
    border-radius: 20px;
  }

  .tier-chip.gold { background: #fef3c7; color: #f59e0b; }
  .tier-chip.silver { background: #f3f4f6; color: #6b7280; }
  .tier-chip.bronze { background: #ffedd5; color: #ea580c; }

  /* Winners Table */
  .winners-table {
    overflow-x: auto;
  }

  .table-header {
    display: grid;
    grid-template-columns: 1.5fr 1.5fr 1fr 1fr 0.8fr;
    padding: 10px 0;
    border-bottom: 1px solid #e8e4dc;
    font-size: 11px;
    font-weight: 600;
    color: #9b8f7e;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }

  .table-body {
    display: flex;
    flex-direction: column;
  }

  .table-row {
    display: grid;
    grid-template-columns: 1.5fr 1.5fr 1fr 1fr 0.8fr;
    padding: 12px 0;
    border-bottom: 1px solid #f0ebe3;
    align-items: center;
    font-size: 13px;
  }

  .table-row:hover {
    background: #fefdfb;
  }

  .winner-cell {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .winner-avatar-sm {
    width: 28px;
    height: 28px;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .prize-cell {
    color: #4a3e2e;
  }

  .amount-cell {
    font-weight: 600;
    color: #2c2418;
  }

  .date-cell {
    font-size: 12px;
    color: #b8ab9a;
  }

  .tier-cell {
    font-size: 10px;
    font-weight: 600;
    padding: 3px 8px;
    border-radius: 20px;
    display: inline-block;
    width: fit-content;
  }

  .tier-cell.gold { background: #fef3c7; color: #f59e0b; }
  .tier-cell.silver { background: #f3f4f6; color: #6b7280; }
  .tier-cell.bronze { background: #ffedd5; color: #ea580c; }

  @media (max-width: 700px) {
    .table-header {
      display: none;
    }
    
    .table-row {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      padding: 12px;
    }
    
    .winner-cell {
      width: 100%;
    }
    
    .prize-cell, .amount-cell, .date-cell, .tier-cell {
      font-size: 12px;
    }
    
    .tier-cell {
      margin-left: auto;
    }
  }

  @media (max-width: 480px) {
    .content {
      padding: 16px;
    }
    
    .payout-amount {
      font-size: 36px;
    }
    
    .podium-card {
      padding: 12px;
    }
    
    .podium-amount {
      font-size: 16px;
    }
  }
</style>