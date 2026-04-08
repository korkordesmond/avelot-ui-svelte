<script lang="ts">
  import { CircleUser, Bell, ArrowUpRight, ArrowDownLeft, Trophy, TrendingUp, Shield, Clock, Gift, Users, ChevronRight, Sparkles, Star, Zap, Award } from 'lucide-svelte';

  const deals = [
    { name: "This Week's Prize", amount: 96400, endsIn: new Date('2026-04-15'), participants: 1240 },
    { name: 'Monthly Mega Draw', amount: 1480000, endsIn: new Date('2026-04-10'), participants: 8450 },
    { name: 'Quarterly Jackpot', amount: 10800000, endsIn: new Date('2026-05-01'), participants: 23100 },
    { name: 'Summer Jackpot', amount: 34500000, endsIn: new Date('2026-08-03'), participants: 15700 },
    { name: 'Half-year Grand Prize', amount: 57600000, endsIn: new Date('2026-12-11'), participants: 8920 },
    { name: 'Annual Grand Prize', amount: 500000, endsIn: new Date('2026-12-12'), participants: 3400 },
  ];

  function formatAmount(amount: number): string {
    if (amount >= 1_000_000) {
      return `$${(amount / 1_000_000).toFixed(1)}M`;
    }
    if (amount >= 1_000) {
      return `$${(amount / 1_000).toFixed(0)}K`;
    }
    return `$${amount.toLocaleString()}`;
  }

  function getTimeRemaining(endsIn: Date): string {
    const now = new Date();
    const diff = endsIn.getTime() - now.getTime();
    const days = Math.ceil(diff / (1000 * 60 * 60 * 24));
    if (days <= 0) return 'Ended';
    if (days === 1) return '1 day left';
    return `${days} days left`;
  }
</script>

<div class="page">
  <!-- Header -->
  <header class="topbar">
    <div class="topbar-inner">
      <a href="/profile" class="profile-link">
        <div class="avatar-wrap">
          <div class="avatar-gradient">
            <CircleUser size={36} class="avatar-icon" />
          </div>
          <div class="verified-dot">
            <Shield size={10} class="verified-icon" />
          </div>
        </div>
        <div class="profile-text">
          <span class="profile-name">Jane Cooper</span>
          <div class="profile-badge-group">
            <span class="profile-badge">Verified Member</span>
            <span class="profile-level">✦ Lvl 12</span>
          </div>
        </div>
      </a>
      <div class="header-actions">
        <a href="/notifications" class="notif-btn">
          <Bell size={20} />
          <span class="notif-dot"></span>
        </a>
      </div>
    </div>
  </header>

  <main class="content">
    <!-- Balance Card -->
    <div class="balance-card">
      <div class="balance-header">
        <div class="balance-label">
          <TrendingUp size={13} />
          <span>Total Balance</span>
        </div>
        <div class="balance-chip">
          <Sparkles size={10} />
          <span>Ready to play</span>
        </div>
      </div>
      <div class="balance-amount">$2,500,000</div>
      <div class="balance-sub">≈ 2,000,000 pts · Available</div>
      <div class="balance-actions">
        <a href="/wallet/deposit" class="action-btn deposit">
          <ArrowDownLeft size={18} />
          <span>Deposit</span>
        </a>
        <a href="/wallet/withdraw" class="action-btn withdraw">
          <ArrowUpRight size={18} />
          <span>Withdraw</span>
        </a>
      </div>
    </div>

    <!-- Quick Stats Row -->
    <div class="stats-row">
      <div class="stat-card">
        <div class="stat-icon trophy">
          <Trophy size={18} />
        </div>
        <div class="stat-info">
          <span class="stat-value">6</span>
          <span class="stat-label">Active Prizes</span>
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon users">
          <Users size={18} />
        </div>
        <div class="stat-info">
          <span class="stat-value">61.2K</span>
          <span class="stat-label">Participants</span>
        </div>
      </div>
      <div class="stat-card">
        <div class="stat-icon pool">
          <Zap size={18} />
        </div>
        <div class="stat-info">
          <span class="stat-value">$82.4M</span>
          <span class="stat-label">Total Pool</span>
        </div>
      </div>
    </div>

    <!-- Featured Section -->
    <div class="featured-card">
      <div class="featured-content">
        <div class="featured-badge">
          <Award size={14} />
          <span>Featured</span>
        </div>
        <h3>Quarterly Jackpot</h3>
        <p class="featured-amount">$10.8M</p>
        <p class="featured-desc">The biggest draw of the season is here!</p>
        <button class="featured-btn">Enter Now →</button>
      </div>
      <div class="featured-glow"></div>
    </div>

    <!-- Prizes Section -->
    <section class="prizes-section">
      <div class="prizes-header">
        <div class="prizes-title-group">
          <div class="title-icon">
            <Trophy size={20} />
          </div>
          <div>
            <h2 class="prizes-title">Available Prizes</h2>
            <p class="prizes-sub">Join draws to win amazing rewards</p>
          </div>
        </div>
        <div class="header-badges">
          <span class="active-badge">
            <Sparkles size={12} />
            {deals.length} Active
          </span>
          <a href="/prizes" class="view-all-link">
            View all
            <ChevronRight size={14} />
          </a>
        </div>
      </div>

      <div class="prizes-grid">
        {#each deals as deal (deal.name)}
          <div class="prize-card">
            <div class="prize-card-inner">
              <div class="prize-top">
                <span class="prize-name">{deal.name}</span>
                <span class="prize-badge">{deal.participants.toLocaleString()} joining</span>
              </div>
              <div class="prize-amount">{formatAmount(deal.amount)}</div>
              <div class="prize-bottom">
                <div class="prize-time">
                  <Clock size={12} />
                  <span>{getTimeRemaining(deal.endsIn)}</span>
                </div>
                <button class="join-btn">Join →</button>
              </div>
            </div>
          </div>
        {/each}
      </div>

      <div class="prizes-footer">
        <div class="footer-note">
          <Gift size={13} />
          <span>New draws added every week — stay tuned!</span>
        </div>
      </div>
    </section>
  </main>
</div>

<style>
  .page {
    min-height: 100vh;
    background: #f5f2eb;
    padding-bottom: 5rem;
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

  .profile-link {
    display: flex;
    align-items: center;
    gap: 10px;
    text-decoration: none;
    transition: opacity 0.15s;
  }

  .profile-link:hover {
    opacity: 0.75;
  }

  .avatar-wrap {
    position: relative;
  }

  .avatar-gradient {
    width: 44px;
    height: 44px;
    border-radius: 50%;
    background: linear-gradient(135deg, #f59e0b, #ea580c);
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .avatar-icon {
    color: white;
    opacity: 0.9;
  }

  .verified-dot {
    position: absolute;
    bottom: -2px;
    right: -2px;
    background: #10b981;
    border-radius: 50%;
    width: 16px;
    height: 16px;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 2px solid #faf8f4;
  }

  .verified-icon {
    color: white;
  }

  .profile-text {
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .profile-name {
    font-size: 14px;
    font-weight: 600;
    color: #2c2418;
    line-height: 1;
  }

  .profile-badge-group {
    display: flex;
    gap: 6px;
    align-items: center;
  }

  .profile-badge {
    font-size: 10px;
    color: #10b981;
    font-weight: 500;
    background: rgba(16, 185, 129, 0.1);
    padding: 2px 8px;
    border-radius: 20px;
  }

  .profile-level {
    font-size: 10px;
    color: #f59e0b;
    font-weight: 500;
    background: rgba(245, 158, 11, 0.1);
    padding: 2px 8px;
    border-radius: 20px;
  }

  .header-actions {
    display: flex;
    gap: 8px;
  }

  .notif-btn {
    position: relative;
    padding: 8px;
    border-radius: 50%;
    color: #8b7f6e;
    transition: background 0.15s;
    display: flex;
  }

  .notif-btn:hover {
    background: #f0ebe3;
  }

  .notif-dot {
    position: absolute;
    top: 6px;
    right: 6px;
    width: 7px;
    height: 7px;
    background: #ef4444;
    border: 1.5px solid #faf8f4;
    border-radius: 50%;
  }

  /* Content */
  .content {
    max-width: 1100px;
    margin: 0 auto;
    padding: 24px 20px 0;
  }

  /* Balance Card */
  .balance-card {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 24px;
    padding: 28px 24px;
    text-align: center;
    margin-bottom: 20px;
  }

  .balance-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 16px;
  }

  .balance-label {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    font-size: 11px;
    font-weight: 500;
    color: #9b8f7e;
    background: #f0ebe3;
    border-radius: 40px;
    padding: 4px 12px;
  }

  .balance-chip {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 10px;
    font-weight: 500;
    color: #f59e0b;
    background: #fef3c7;
    border-radius: 40px;
    padding: 4px 10px;
  }

  .balance-amount {
    font-size: 48px;
    font-weight: 600;
    color: #2c2418;
    letter-spacing: -0.02em;
    line-height: 1;
    margin-bottom: 8px;
  }

  .balance-sub {
    font-size: 12px;
    color: #b8ab9a;
    margin-bottom: 24px;
  }

  .balance-actions {
    display: flex;
    gap: 12px;
    justify-content: center;
    max-width: 320px;
    margin: 0 auto;
  }

  .action-btn {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    border-radius: 40px;
    padding: 11px 16px;
    font-size: 13px;
    font-weight: 500;
    text-decoration: none;
    transition: all 0.2s;
  }

  .deposit {
    background: #d1fae5;
    color: #065f46;
  }

  .deposit:hover {
    background: #a7f3d0;
    transform: translateY(-1px);
  }

  .withdraw {
    background: #fee2e2;
    color: #991b1b;
  }

  .withdraw:hover {
    background: #fecaca;
    transform: translateY(-1px);
  }

  /* Stats Row */
  .stats-row {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 12px;
    margin-bottom: 24px;
  }

  .stat-card {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 16px;
    padding: 14px 16px;
    display: flex;
    align-items: center;
    gap: 12px;
    transition: all 0.2s;
  }

  .stat-card:hover {
    border-color: #ddd6cc;
  }

  .stat-icon {
    width: 40px;
    height: 40px;
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .stat-icon.trophy {
    background: #fef3c7;
    color: #f59e0b;
  }

  .stat-icon.users {
    background: #dbeafe;
    color: #3b82f6;
  }

  .stat-icon.pool {
    background: #ede9fe;
    color: #8b5cf6;
  }

  .stat-info {
    display: flex;
    flex-direction: column;
  }

  .stat-value {
    font-size: 20px;
    font-weight: 600;
    color: #2c2418;
    line-height: 1.2;
  }

  .stat-label {
    font-size: 11px;
    color: #b8ab9a;
    font-weight: 500;
  }

  /* Featured Card */
  .featured-card {
    background: linear-gradient(135deg, #fefcf8, #fffbeb);
    border: 1px solid #fde68a;
    border-radius: 20px;
    padding: 20px;
    margin-bottom: 24px;
    position: relative;
    overflow: hidden;
  }

  .featured-glow {
    position: absolute;
    top: -50%;
    right: -20%;
    width: 200px;
    height: 200px;
    background: radial-gradient(circle, rgba(245, 158, 11, 0.15), transparent);
    border-radius: 50%;
  }

  .featured-content {
    position: relative;
    z-index: 1;
  }

  .featured-badge {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    padding: 4px 10px;
    background: #fef3c7;
    border-radius: 40px;
    font-size: 10px;
    font-weight: 500;
    color: #f59e0b;
    margin-bottom: 12px;
  }

  .featured-card h3 {
    font-size: 18px;
    font-weight: 600;
    color: #2c2418;
    margin: 0 0 4px;
  }

  .featured-amount {
    font-size: 28px;
    font-weight: 700;
    color: #f59e0b;
    margin: 0 0 8px;
  }

  .featured-desc {
    font-size: 12px;
    color: #9b8f7e;
    margin: 0 0 16px;
  }

  .featured-btn {
    padding: 8px 20px;
    background: linear-gradient(135deg, #f59e0b, #ea580c);
    border: none;
    border-radius: 40px;
    font-size: 12px;
    font-weight: 500;
    color: white;
    cursor: pointer;
    transition: all 0.2s;
  }

  .featured-btn:hover {
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(245, 158, 11, 0.3);
  }

  /* Prizes Section */
  .prizes-section {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 24px;
    padding: 24px;
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
    gap: 12px;
  }

  .title-icon {
    width: 40px;
    height: 40px;
    background: #fef3c7;
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #f59e0b;
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

  .header-badges {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .active-badge {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 11px;
    font-weight: 500;
    color: #9b8f7e;
    background: #f0ebe3;
    border-radius: 40px;
    padding: 4px 12px;
  }

  .view-all-link {
    display: flex;
    align-items: center;
    gap: 4px;
    font-size: 11px;
    font-weight: 500;
    color: #9b8f7e;
    text-decoration: none;
    transition: color 0.2s;
  }

  .view-all-link:hover {
    color: #f59e0b;
  }

  /* Prize Cards */
  .prizes-grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 12px;
    margin-bottom: 20px;
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
    transform: translateY(-2px);
  }

  .prize-card-inner {
    padding: 16px;
  }

  .prize-top {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
    flex-wrap: wrap;
    gap: 8px;
  }

  .prize-name {
    font-size: 14px;
    font-weight: 600;
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
    font-size: 24px;
    font-weight: 600;
    color: #2c2418;
    letter-spacing: -0.02em;
    margin-bottom: 12px;
  }

  .prize-bottom {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding-top: 10px;
    border-top: 1px solid #e8e4dc;
  }

  .prize-time {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 10px;
    color: #b8ab9a;
  }

  .join-btn {
    background: none;
    border: none;
    font-size: 11px;
    font-weight: 500;
    color: #2c2418;
    cursor: pointer;
    padding: 4px 0;
    transition: all 0.2s;
    font-family: inherit;
  }

  .join-btn:hover {
    color: #f59e0b;
    transform: translateX(2px);
  }

  .prizes-footer {
    padding-top: 16px;
    border-top: 1px solid #e8e4dc;
  }

  .footer-note {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    font-size: 11px;
    color: #b8ab9a;
  }

  @media (min-width: 600px) {
    .prizes-grid {
      grid-template-columns: repeat(2, 1fr);
    }
    .page{
      padding-bottom: 7rem;
    }
  }

  @media (min-width: 900px) {
    .prizes-grid {
      grid-template-columns: repeat(3, 1fr);
    }
  }

  @media (max-width: 600px) {
    .stats-row {
      grid-template-columns: 1fr;
    }
    
    .balance-actions {
      flex-direction: column;
      max-width: 100%;
    }
    
    .prizes-section {
      padding: 18px;
    }
    
    .balance-amount {
      font-size: 36px;
    }
    
    .featured-amount {
      font-size: 24px;
    }
  }
</style>