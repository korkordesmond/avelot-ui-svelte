<script lang="ts">
  import { goto } from '$app/navigation';
  import { 
    ArrowLeft,
    User,
    CreditCard,
    Shield,
    BadgeCheck,
    Headphones,
    Smartphone,
    Info,
    LogOut,
    ChevronRight,
    Camera,
    Mail,
    Phone,
    Calendar,
    MapPin,
    Copy,
    Check,
    Wallet,
    Gift,
    Sparkles,
    Star,
    Settings,
    Lock,
    Globe
  } from 'lucide-svelte';
  
  // User data
  let user = $state({
    name: 'Jane Smith',
    email: 'jane.smith@example.com',
    phone: '+1 (555) 123-4567',
    memberSince: 'January 2024',
    location: 'New York, USA',
    avatar: null as string | null,
    verified: true,
    tier: 'Gold',
    points: 12500
  });
  
  // Payment methods
  let paymentMethods = $state([
    { id: 1, type: 'Visa', last4: '4242', expiry: '12/26', isDefault: true },
    { id: 2, type: 'Mastercard', last4: '8888', expiry: '08/25', isDefault: false }
  ]);
  
  // UI state
  let showLogoutConfirm = $state(false);
  let copiedEmail = $state(false);
  
  function copyToClipboard(text: string) {
    navigator.clipboard.writeText(text);
    copiedEmail = true;
    setTimeout(() => { copiedEmail = false; }, 2000);
  }
  
  function handleLogout() {
    console.log('Logging out...');
    goto('/');
  }
  
  function removePaymentMethod(id: number) {
    paymentMethods = paymentMethods.filter(m => m.id !== id);
  }
</script>

<div class="page">
  <!-- Header -->
  <header class="topbar">
    <div class="topbar-inner">
      <div class="header-left">
        <button onclick={() => goto('/')} class="back-btn">
          <ArrowLeft size={20} />
        </button>
        <div class="logo-area">
          <div class="icon-wrap">
            <User size={20} />
          </div>
          <div>
            <h1>Profile</h1>
            <p>Manage your account</p>
          </div>
        </div>
      </div>
      <div class="header-right">
        <div class="stats-badge">
          <Star size={14} />
          <span>{user.tier}</span>
        </div>
      </div>
    </div>
  </header>

  <main class="content">
    <!-- Profile Header -->
    <div class="profile-card">
      <div class="profile-avatar">
        <div class="avatar-initial">
          <span>{user.name.charAt(0)}</span>
        </div>
      </div>
      
      <div class="profile-info">
        <div class="profile-name-group">
          <h2>{user.name}</h2>
          {#if user.verified}
            <div class="verified-badge">
              <BadgeCheck size={12} />
              <span>Verified</span>
            </div>
          {/if}
        </div>
        
        <div class="profile-details">
          <div class="detail-item">
            <Mail size={12} />
            <span>{user.email}</span>
            <button onclick={() => copyToClipboard(user.email)} class="copy-btn">
              {#if copiedEmail}
                <Check size={10} class="text-emerald-500" />
              {:else}
                <Copy size={10} class="text-gray-400" />
              {/if}
            </button>
          </div>
          <div class="detail-item">
            <Phone size={12} />
            <span>{user.phone}</span>
          </div>
        </div>
        
        <div class="profile-meta">
          <div class="meta-item">
            <Calendar size={11} />
            <span>Joined {user.memberSince}</span>
          </div>
          <div class="meta-dot">•</div>
          <div class="meta-item">
            <MapPin size={11} />
            <span>{user.location}</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Points Card -->
    <div class="points-card">
      <div class="points-icon">
        <Sparkles size={18} />
      </div>
      <div class="points-info">
        <span class="points-label">Reward Points</span>
        <span class="points-value">{user.points.toLocaleString()} pts</span>
      </div>
      <button class="points-btn">Redeem →</button>
    </div>

    <!-- Payment Methods -->
    <section class="settings-section">
      <div class="section-header">
        <div class="section-title">
          <CreditCard size={18} class="title-icon" />
          <div>
            <h3>Payment Methods</h3>
            <p>Manage your cards and accounts</p>
          </div>
        </div>
        <button class="add-btn">+ Add New</button>
      </div>
      
      <div class="payment-list">
        {#each paymentMethods as method}
          <div class="payment-item">
            <div class="payment-left">
              <div class="payment-icon">
                <CreditCard size={16} />
              </div>
              <div>
                <div class="payment-type">
                  {method.type} •••• {method.last4}
                </div>
                <div class="payment-expiry">Expires {method.expiry}</div>
              </div>
            </div>
            <div class="payment-right">
              {#if method.isDefault}
                <span class="default-badge">Default</span>
              {/if}
              <button 
                onclick={() => removePaymentMethod(method.id)}
                class="remove-btn"
              >
                Remove
              </button>
            </div>
          </div>
        {/each}
      </div>
    </section>

    <!-- Settings Sections -->
    <section class="settings-section">
      <div class="section-header">
        <div class="section-title">
          <Settings size={18} class="title-icon" />
          <div>
            <h3>Settings & Preferences</h3>
            <p>Manage your account settings</p>
          </div>
        </div>
      </div>
      
      <div class="settings-list">
        <button class="setting-item">
          <div class="setting-left">
            <div class="setting-icon">
              <Shield size={16} />
            </div>
            <div>
              <div class="setting-name">Security</div>
              <div class="setting-desc">Password, 2FA, login alerts</div>
            </div>
          </div>
          <ChevronRight size={14} class="setting-arrow" />
        </button>
        
        <button class="setting-item">
          <div class="setting-left">
            <div class="setting-icon">
              <Smartphone size={16} />
            </div>
            <div>
              <div class="setting-name">Linked Devices</div>
              <div class="setting-desc">Manage connected devices</div>
            </div>
          </div>
          <ChevronRight size={14} class="setting-arrow" />
        </button>
        
        <button class="setting-item">
          <div class="setting-left">
            <div class="setting-icon">
              <Globe size={16} />
            </div>
            <div>
              <div class="setting-name">Notifications</div>
              <div class="setting-desc">Email, push, and SMS alerts</div>
            </div>
          </div>
          <ChevronRight size={14} class="setting-arrow" />
        </button>
        
        <button class="setting-item">
          <div class="setting-left">
            <div class="setting-icon">
              <Lock size={16} />
            </div>
            <div>
              <div class="setting-name">Privacy</div>
              <div class="setting-desc">Data preferences and controls</div>
            </div>
          </div>
          <ChevronRight size={14} class="setting-arrow" />
        </button>
      </div>
    </section>

    <!-- Support Section -->
    <section class="settings-section">
      <div class="section-header">
        <div class="section-title">
          <Headphones size={18} class="title-icon" />
          <div>
            <h3>Support</h3>
            <p>Get help when you need it</p>
          </div>
        </div>
      </div>
      
      <div class="settings-list">
        <button class="setting-item">
          <div class="setting-left">
            <div class="setting-icon">
              <Headphones size={16} />
            </div>
            <div>
              <div class="setting-name">Contact Support</div>
              <div class="setting-desc">24/7 customer assistance</div>
            </div>
          </div>
          <ChevronRight size={14} class="setting-arrow" />
        </button>
        
        <button class="setting-item">
          <div class="setting-left">
            <div class="setting-icon">
              <Info size={16} />
            </div>
            <div>
              <div class="setting-name">About Us</div>
              <div class="setting-desc">Company info and terms</div>
            </div>
          </div>
          <ChevronRight size={14} class="setting-arrow" />
        </button>
      </div>
    </section>

    <!-- Logout Section -->
    <div class="logout-section">
      {#if showLogoutConfirm}
        <div class="logout-confirm">
          <p>Are you sure you want to logout?</p>
          <div class="confirm-buttons">
            <button onclick={handleLogout} class="confirm-logout">
              Logout
            </button>
            <button onclick={() => showLogoutConfirm = false} class="confirm-cancel">
              Cancel
            </button>
          </div>
        </div>
      {/if}
      
      <button
        onclick={() => showLogoutConfirm = true}
        class="logout-btn"
      >
        <LogOut size={18} />
        Logout
      </button>
    </div>
  </main>
</div>

<style>
  .page {
    min-height: 100vh;
    background: #f5f2eb;
    font-family: 'DM Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    padding-bottom: 3rem;
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

  .header-left {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .back-btn {
    padding: 8px;
    border-radius: 50%;
    color: #9b8f7e;
    cursor: pointer;
    transition: all 0.2s;
    background: none;
    border: none;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .back-btn:hover {
    background: #f0ebe3;
    color: #4a3e2e;
  }

  .logo-area {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .icon-wrap {
    width: 40px;
    height: 40px;
    background: linear-gradient(135deg, #f59e0b, #ea580c);
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
  }

  .logo-area h1 {
    font-size: 16px;
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

  .header-right {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .stats-badge {
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 6px 12px;
    background: #fef3c7;
    border-radius: 40px;
    font-size: 12px;
    font-weight: 500;
    color: #f59e0b;
  }

  /* Content */
  .content {
    max-width: 680px;
    margin: 0 auto;
    padding: 20px;
  }

  /* Profile Card */
  .profile-card {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 20px;
    padding: 24px;
    text-align: center;
    margin-bottom: 16px;
  }

  .profile-avatar {
    margin-bottom: 16px;
  }

  .avatar-initial {
    width: 80px;
    height: 80px;
    margin: 0 auto;
    background: linear-gradient(135deg, #f59e0b, #ea580c);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .avatar-initial span {
    font-size: 32px;
    font-weight: 600;
    color: white;
  }

  .profile-name-group {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-bottom: 12px;
    flex-wrap: wrap;
  }

  .profile-name-group h2 {
    font-size: 20px;
    font-weight: 600;
    color: #2c2418;
    margin: 0;
  }

  .verified-badge {
    display: inline-flex;
    align-items: center;
    gap: 4px;
    padding: 2px 8px;
    background: #d1fae5;
    border-radius: 40px;
  }

  .verified-badge span {
    font-size: 10px;
    font-weight: 500;
    color: #10b981;
  }

  .profile-details {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 8px;
    margin-bottom: 12px;
  }

  .detail-item {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 13px;
    color: #6b5f4e;
  }

  .copy-btn {
    padding: 2px;
    background: none;
    border: none;
    cursor: pointer;
    display: flex;
    align-items: center;
  }

  .profile-meta {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 6px;
    font-size: 11px;
    color: #b8ab9a;
  }

  .meta-dot {
    color: #e8e4dc;
  }

  /* Points Card */
  .points-card {
    background: linear-gradient(135deg, #fefcf8, #fffbeb);
    border: 1px solid #fde68a;
    border-radius: 16px;
    padding: 14px 18px;
    display: flex;
    align-items: center;
    gap: 14px;
    margin-bottom: 20px;
  }

  .points-icon {
    width: 40px;
    height: 40px;
    background: #fef3c7;
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #f59e0b;
  }

  .points-info {
    flex: 1;
  }

  .points-label {
    font-size: 11px;
    color: #9b8f7e;
    display: block;
  }

  .points-value {
    font-size: 16px;
    font-weight: 600;
    color: #2c2418;
  }

  .points-btn {
    padding: 6px 14px;
    background: #fef3c7;
    border: none;
    border-radius: 40px;
    font-size: 12px;
    font-weight: 500;
    color: #f59e0b;
    cursor: pointer;
    transition: all 0.2s;
  }

  .points-btn:hover {
    background: #fde68a;
  }

  /* Settings Sections */
  .settings-section {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 20px;
    margin-bottom: 16px;
    overflow: hidden;
  }

  .section-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 16px 18px;
    border-bottom: 1px solid #e8e4dc;
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

  .section-title h3 {
    font-size: 14px;
    font-weight: 600;
    color: #2c2418;
    margin: 0 0 2px;
  }

  .section-title p {
    font-size: 11px;
    color: #b8ab9a;
    margin: 0;
  }

  .add-btn {
    padding: 6px 14px;
    background: #f0ebe3;
    border: none;
    border-radius: 40px;
    font-size: 12px;
    font-weight: 500;
    color: #4a3e2e;
    cursor: pointer;
    transition: all 0.2s;
  }

  .add-btn:hover {
    background: #e8e0d6;
  }

  /* Payment List */
  .payment-list {
    padding: 4px 0;
  }

  .payment-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 14px 18px;
    border-bottom: 1px solid #f0ebe3;
    flex-wrap: wrap;
    gap: 10px;
  }

  .payment-item:last-child {
    border-bottom: none;
  }

  .payment-left {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .payment-icon {
    width: 36px;
    height: 36px;
    background: #f0ebe3;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #9b8f7e;
  }

  .payment-type {
    font-size: 13px;
    font-weight: 500;
    color: #2c2418;
  }

  .payment-expiry {
    font-size: 10px;
    color: #b8ab9a;
  }

  .payment-right {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .default-badge {
    font-size: 10px;
    font-weight: 500;
    padding: 3px 8px;
    background: #d1fae5;
    color: #10b981;
    border-radius: 20px;
  }

  .remove-btn {
    font-size: 11px;
    color: #b8ab9a;
    background: none;
    border: none;
    cursor: pointer;
    transition: color 0.2s;
  }

  .remove-btn:hover {
    color: #ef4444;
  }

  /* Settings List */
  .settings-list {
    padding: 4px 0;
  }

  .setting-item {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 14px 18px;
    background: none;
    border: none;
    border-bottom: 1px solid #f0ebe3;
    cursor: pointer;
    transition: background 0.2s;
  }

  .setting-item:last-child {
    border-bottom: none;
  }

  .setting-item:hover {
    background: #fefdfb;
  }

  .setting-left {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .setting-icon {
    width: 36px;
    height: 36px;
    background: #f0ebe3;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #9b8f7e;
  }

  .setting-name {
    font-size: 13px;
    font-weight: 500;
    color: #2c2418;
    text-align: left;
  }

  .setting-desc {
    font-size: 10px;
    color: #b8ab9a;
    text-align: left;
  }

  .setting-arrow {
    color: #d1cbbc;
  }

  /* Logout Section */
  .logout-section {
    margin-top: 24px;
  }

  .logout-confirm {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 16px;
    padding: 16px;
    margin-bottom: 12px;
  }

  .logout-confirm p {
    font-size: 13px;
    color: #2c2418;
    margin-bottom: 14px;
    text-align: center;
  }

  .confirm-buttons {
    display: flex;
    gap: 12px;
  }

  .confirm-logout {
    flex: 1;
    padding: 10px;
    background: #ef4444;
    border: none;
    border-radius: 40px;
    font-size: 13px;
    font-weight: 500;
    color: white;
    cursor: pointer;
    transition: background 0.2s;
  }

  .confirm-logout:hover {
    background: #dc2626;
  }

  .confirm-cancel {
    flex: 1;
    padding: 10px;
    background: #f0ebe3;
    border: none;
    border-radius: 40px;
    font-size: 13px;
    font-weight: 500;
    color: #4a3e2e;
    cursor: pointer;
    transition: background 0.2s;
  }

  .confirm-cancel:hover {
    background: #e8e0d6;
  }

  .logout-btn {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    padding: 14px;
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 60px;
    font-size: 14px;
    font-weight: 500;
    color: #ef4444;
    cursor: pointer;
    transition: all 0.2s;
  }

  .logout-btn:hover {
    background: #fef2f2;
    border-color: #fecaca;
  }

  @media (max-width: 480px) {
    .content {
      padding: 16px;
    }

    .topbar-inner {
      padding: 10px 16px;
    }

    .profile-card {
      padding: 20px;
    }

    .profile-name-group h2 {
      font-size: 18px;
    }

    .section-header {
      flex-direction: column;
      align-items: flex-start;
    }

    .add-btn {
      align-self: flex-start;
    }

    .payment-item {
      flex-direction: column;
      align-items: flex-start;
    }

    .payment-right {
      padding-left: 48px;
    }
  }
</style>