<script lang="ts">
  import { ArrowRight, Timer, Gift, Trophy, Sparkles, Users, Clock } from 'lucide-svelte';

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

  const prizeTier = $derived(
    amount >= 10000000 ? 'jackpot' : amount >= 1000000 ? 'mega' : 'standard'
  );

  function getTierColor() {
    switch(prizeTier) {
      case 'jackpot': return 'gold';
      case 'mega': return 'purple';
      default: return 'blue';
    }
  }

  function formatAmountShort(amount: number): string {
    if (amount >= 1_000_000) {
      return `$${(amount / 1_000_000).toFixed(1)}M`;
    }
    if (amount >= 1_000) {
      return `$${(amount / 1_000).toFixed(0)}K`;
    }
    return `$${amount.toLocaleString()}`;
  }
</script>

<article class="prize-card">
  <div class="prize-card-inner">
    <!-- Header -->
    <div class="prize-header">
      <div class="prize-title-group">
        <div class="prize-icon {prizeTier}">
          {#if prizeTier === 'jackpot'}
            <Trophy size={14} />
          {:else if prizeTier === 'mega'}
            <Sparkles size={14} />
          {:else}
            <Gift size={14} />
          {/if}
        </div>
        <span class="prize-name">{name}</span>
      </div>
      <div class="days-pill {daysLeft <= 3 ? 'urgent' : ''}">
        <Clock size={10} />
        <span>{daysLeft} {daysLeft === 1 ? 'day' : 'days'} left</span>
      </div>
    </div>

    <!-- Prize Amount -->
    <div class="prize-amount-wrapper">
      <div class="prize-amount">{formattedAmountShort(amount)}</div>
      <div class="prize-tier-badge {prizeTier}">
        {#if prizeTier === 'jackpot'}
          Jackpot
        {:else if prizeTier === 'mega'}
          Mega Draw
        {:else}
          Standard Draw
        {/if}
      </div>
    </div>

    <!-- Progress Section -->
    <div class="progress-section">
      <div class="progress-header">
        <span>Time remaining</span>
        <span class="days-remaining">{daysLeft} days</span>
      </div>
      <div class="progress-track">
        <div class="progress-fill {prizeTier}" style="width: {Math.min(100, (daysLeft / 30) * 100)}%"></div>
      </div>
    </div>

    <!-- Enter Button -->
    <a href="/draws/{name.toLowerCase().replace(/\s+/g, '-')}" class="enter-btn {prizeTier}">
      <span>Enter draw</span>
      <div class="arrow-circle">
        <ArrowRight size={12} />
      </div>
    </a>

    <!-- Stats Footer -->
    <div class="card-footer">
      <div class="stat-item">
        <Users size={10} />
        <span>1,240 joining</span>
      </div>
    </div>
  </div>
</article>

<style>
  .prize-card {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 20px;
    transition: all 0.2s ease;
    overflow: hidden;
  }

  .prize-card:hover {
    border-color: #ddd6cc;
    background: #ffffff;
    transform: translateY(-2px);
  }

  .prize-card-inner {
    padding: 18px;
  }

  /* Header */
  .prize-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 14px;
    flex-wrap: wrap;
    gap: 10px;
  }

  .prize-title-group {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .prize-icon {
    width: 28px;
    height: 28px;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .prize-icon.jackpot {
    background: #fef3c7;
    color: #f59e0b;
  }

  .prize-icon.mega {
    background: #ede9fe;
    color: #8b5cf6;
  }

  .prize-icon.standard {
    background: #dbeafe;
    color: #3b82f6;
  }

  .prize-name {
    font-size: 14px;
    font-weight: 600;
    color: #2c2418;
  }

  .days-pill {
    display: flex;
    align-items: center;
    gap: 5px;
    padding: 4px 10px;
    background: #f0ebe3;
    border-radius: 40px;
    font-size: 10px;
    font-weight: 500;
    color: #9b8f7e;
  }

  .days-pill.urgent {
    background: #fee2e2;
    color: #ef4444;
  }

  /* Prize Amount */
  .prize-amount-wrapper {
    display: flex;
    align-items: baseline;
    justify-content: space-between;
    margin-bottom: 16px;
    flex-wrap: wrap;
    gap: 8px;
  }

  .prize-amount {
    font-size: 28px;
    font-weight: 700;
    color: #2c2418;
    letter-spacing: -0.02em;
  }

  .prize-tier-badge {
    font-size: 10px;
    font-weight: 600;
    padding: 4px 10px;
    border-radius: 40px;
  }

  .prize-tier-badge.jackpot {
    background: #fef3c7;
    color: #f59e0b;
  }

  .prize-tier-badge.mega {
    background: #ede9fe;
    color: #8b5cf6;
  }

  .prize-tier-badge.standard {
    background: #dbeafe;
    color: #3b82f6;
  }

  /* Progress Section */
  .progress-section {
    margin-bottom: 16px;
  }

  .progress-header {
    display: flex;
    justify-content: space-between;
    font-size: 10px;
    color: #b8ab9a;
    margin-bottom: 6px;
  }

  .days-remaining {
    font-weight: 500;
    color: #2c2418;
  }

  .progress-track {
    height: 4px;
    background: #f0ebe3;
    border-radius: 10px;
    overflow: hidden;
  }

  .progress-fill {
    height: 100%;
    border-radius: 10px;
    transition: width 0.3s ease;
  }

  .progress-fill.jackpot {
    background: linear-gradient(90deg, #f59e0b, #fbbf24);
  }

  .progress-fill.mega {
    background: linear-gradient(90deg, #8b5cf6, #a78bfa);
  }

  .progress-fill.standard {
    background: linear-gradient(90deg, #3b82f6, #60a5fa);
  }

  /* Enter Button */
  .enter-btn {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 10px 14px;
    background: #f0ebe3;
    border-radius: 12px;
    text-decoration: none;
    transition: all 0.2s;
    margin-bottom: 12px;
  }

  .enter-btn:hover {
    background: #e8e0d6;
  }

  .enter-btn span {
    font-size: 12px;
    font-weight: 500;
    color: #2c2418;
  }

  .arrow-circle {
    width: 24px;
    height: 24px;
    background: #d1cbbc;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #2c2418;
    transition: all 0.2s;
  }

  .enter-btn:hover .arrow-circle {
    transform: translateX(2px);
    background: #b8ab9a;
  }

  /* Card Footer */
  .card-footer {
    display: flex;
    align-items: center;
    gap: 12px;
    padding-top: 10px;
    border-top: 1px solid #e8e4dc;
  }

  .stat-item {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 10px;
    color: #b8ab9a;
  }

  /* Responsive */
  @media (max-width: 480px) {
    .prize-card-inner {
      padding: 14px;
    }

    .prize-amount {
      font-size: 22px;
    }

    .prize-name {
      font-size: 13px;
    }
  }
</style>