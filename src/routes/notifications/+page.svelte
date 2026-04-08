<script lang="ts">
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';
  import { 
    Bell, 
    ChevronLeft, 
    CheckCircle, 
    AlertCircle, 
    Info, 
    Trophy, 
    Wallet, 
    CheckCheck,
    Trash2,
    Clock,
    X,
    Sparkles,
    Gift
  } from 'lucide-svelte';
  
  // Types
  type NotificationType = 'success' | 'warning' | 'info' | 'prize' | 'transaction';
  
  interface Notification {
    id: string;
    title: string;
    message: string;
    type: NotificationType;
    timestamp: Date;
    read: boolean;
    actionUrl?: string;
  }
  
  // State
  let notifications: Notification[] = $state([]);
  let isLoading: boolean = $state(true);
  let filter: 'all' | 'unread' = $state('all');
  
  // Load notifications
  async function loadNotifications() {
    isLoading = true;
    await new Promise(resolve => setTimeout(resolve, 800));
    
    notifications = [
      {
        id: '1',
        title: 'Weekly Prize Draw Winner!',
        message: 'Congratulations! You won $96,400.',
        type: 'prize',
        timestamp: new Date(Date.now() - 1000 * 60 * 30),
        read: false,
        actionUrl: '/winners'
      },
      {
        id: '2',
        title: 'Deposit Successful',
        message: 'Your deposit of $5,000 has been processed.',
        type: 'transaction',
        timestamp: new Date(Date.now() - 1000 * 60 * 60 * 2),
        read: false,
        actionUrl: '/wallet/deposit'
      },
      {
        id: '3',
        title: 'New Jackpot Alert!',
        message: 'Quarterly Jackpot has reached $10.8M!',
        type: 'info',
        timestamp: new Date(Date.now() - 1000 * 60 * 60 * 5),
        read: true,
        actionUrl: '/'
      },
      {
        id: '4',
        title: 'Welcome Bonus Credited',
        message: '$500 has been added to your account.',
        type: 'success',
        timestamp: new Date(Date.now() - 1000 * 60 * 60 * 24),
        read: true,
        actionUrl: '/wallet'
      },
      {
        id: '5',
        title: 'Withdrawal Request Received',
        message: 'Your withdrawal of $2,500 is being processed.',
        type: 'transaction',
        timestamp: new Date(Date.now() - 1000 * 60 * 60 * 24 * 2),
        read: true,
        actionUrl: '/wallet/withdraw'
      },
      {
        id: '6',
        title: 'Monthly Mega Draw Reminder',
        message: '3 days left to enter the $1.48M draw!',
        type: 'warning',
        timestamp: new Date(Date.now() - 1000 * 60 * 60 * 24 * 3),
        read: true,
        actionUrl: '/'
      }
    ];
    
    isLoading = false;
  }
  
  function markAsRead(id: string) {
    const notification = notifications.find(n => n.id === id);
    if (notification && !notification.read) {
      notification.read = true;
    }
  }
  
  function markAllAsRead() {
    notifications.forEach(n => {
      if (!n.read) n.read = true;
    });
  }
  
  function deleteNotification(id: string) {
    notifications = notifications.filter(n => n.id !== id);
  }
  
  const unreadCount = $derived(notifications.filter(n => !n.read).length);
  const filteredNotifications = $derived(
    notifications.filter(n => filter === 'all' || !n.read)
  );
  
  function getTypeIcon(type: NotificationType) {
    switch(type) {
      case 'success': return CheckCircle;
      case 'warning': return AlertCircle;
      case 'prize': return Trophy;
      case 'transaction': return Wallet;
      default: return Info;
    }
  }
  
  function getTypeColor(type: NotificationType) {
    switch(type) {
      case 'success': return 'success';
      case 'warning': return 'warning';
      case 'prize': return 'prize';
      case 'transaction': return 'transaction';
      default: return 'info';
    }
  }
  
  function formatTimestamp(date: Date): string {
    const now = new Date();
    const diffMins = Math.floor((now.getTime() - date.getTime()) / 60000);
    const diffHours = Math.floor(diffMins / 60);
    const diffDays = Math.floor(diffHours / 24);
    
    if (diffMins < 1) return 'Just now';
    if (diffMins < 60) return `${diffMins}m`;
    if (diffHours < 24) return `${diffHours}h`;
    if (diffDays < 7) return `${diffDays}d`;
    return date.toLocaleDateString('en-US', { month: 'short', day: 'numeric' });
  }
  
  loadNotifications();
</script>

<div class="page">
  <!-- Header -->
  <header class="topbar">
    <div class="topbar-inner">
      <div class="header-left">
        <button onclick={() => goto('/')} class="back-btn">
          <ChevronLeft size={20} />
        </button>
        <div class="logo-area">
          <div class="icon-wrap">
            <Bell size={20} />
          </div>
          <div>
            <h1>Notifications</h1>
            <p>Stay updated with your activity</p>
          </div>
        </div>
      </div>
      <div class="header-right">
        {#if unreadCount > 0}
          <button onclick={markAllAsRead} class="mark-read-btn" title="Mark all as read">
            <CheckCheck size={18} />
            <span>Mark all</span>
          </button>
        {/if}
        <div class="stats-badge">
          <Bell size={14} />
          <span>{unreadCount} New</span>
        </div>
      </div>
    </div>
  </header>

  <main class="content">
    <!-- Filter Tabs -->
    <div class="filter-tabs">
      <button
        onclick={() => filter = 'all'}
        class="tab-btn {filter === 'all' ? 'active' : ''}"
      >
        All
        <span class="tab-count">{notifications.length}</span>
      </button>
      <button
        onclick={() => filter = 'unread'}
        class="tab-btn {filter === 'unread' ? 'active' : ''}"
      >
        Unread
        {#if unreadCount > 0}
          <span class="tab-count unread">{unreadCount}</span>
        {/if}
      </button>
    </div>

    <!-- Loading State -->
    {#if isLoading}
      <div class="loading-state">
        {#each [1, 2, 3] as i}
          <div class="skeleton-card">
            <div class="skeleton-icon"></div>
            <div class="skeleton-content">
              <div class="skeleton-title"></div>
              <div class="skeleton-message"></div>
            </div>
          </div>
        {/each}
      </div>
    
    {:else if filteredNotifications.length === 0}
      <!-- Empty State -->
      <div class="empty-state">
        <div class="empty-icon">
          <Bell size={28} />
        </div>
        <h3>No notifications</h3>
        <p>{filter === 'unread' ? "You're all caught up!" : "You don't have any notifications yet"}</p>
      </div>
    
    {:else}
      <!-- Notifications List -->
      <div class="notifications-list">
        {#each filteredNotifications as notification (notification.id)}
          {@const Icon = getTypeIcon(notification.type)}
          {@const typeColor = getTypeColor(notification.type)}
          
          <div class="notification-card {notification.read ? 'read' : 'unread'} {typeColor}">
            <div class="notification-icon">
              <Icon size={18} />
            </div>
            
            <div class="notification-content">
              <div class="notification-header">
                <h3>{notification.title}</h3>
                <div class="notification-meta">
                  <span class="notification-time">
                    <Clock size={10} />
                    {formatTimestamp(notification.timestamp)}
                  </span>
                  <button
                    onclick={() => deleteNotification(notification.id)}
                    class="delete-btn"
                    title="Delete"
                  >
                    <X size={12} />
                  </button>
                </div>
              </div>
              <p class="notification-message">{notification.message}</p>
              <div class="notification-footer">
                {#if notification.actionUrl}
                  <a
                    href={notification.actionUrl}
                    onclick={() => markAsRead(notification.id)}
                    class="action-link"
                  >
                    View details →
                  </a>
                {/if}
                {#if !notification.read}
                  <button
                    onclick={() => markAsRead(notification.id)}
                    class="mark-read-link"
                  >
                    Mark as read
                  </button>
                {/if}
              </div>
            </div>
          </div>
        {/each}
      </div>
    {/if}
  </main>
</div>

<style>
  .page {
    min-height: 100vh;
    background: #f5f2eb;
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
    padding: 12px 20px 12px 2px;
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

  .mark-read-btn {
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 6px 12px;
    background: #f0ebe3;
    border: none;
    border-radius: 40px;
    font-size: 12px;
    font-weight: 500;
    color: #4a3e2e;
    cursor: pointer;
    transition: all 0.2s;
  }

  .mark-read-btn:hover {
    background: #e8e0d6;
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

  /* Filter Tabs */
  .filter-tabs {
    display: flex;
    gap: 8px;
    margin-bottom: 20px;
    background: #fefcf8;
    padding: 4px;
    border-radius: 60px;
    border: 1px solid #e8e4dc;
    width: fit-content;
  }

  .tab-btn {
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 8px 20px;
    border: none;
    background: transparent;
    border-radius: 40px;
    font-size: 13px;
    font-weight: 500;
    color: #9b8f7e;
    cursor: pointer;
    transition: all 0.2s;
  }

  .tab-btn.active {
    background: #f0ebe3;
    color: #2c2418;
  }

  .tab-count {
    font-size: 11px;
    padding: 2px 6px;
    background: #e8e4dc;
    border-radius: 20px;
    color: #9b8f7e;
  }

  .tab-count.unread {
    background: #fef3c7;
    color: #f59e0b;
  }

  /* Loading State */
  .loading-state {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  .skeleton-card {
    display: flex;
    gap: 14px;
    padding: 16px;
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 16px;
  }

  .skeleton-icon {
    width: 40px;
    height: 40px;
    background: #f0ebe3;
    border-radius: 12px;
    animation: pulse 1.5s ease-in-out infinite;
  }

  .skeleton-content {
    flex: 1;
  }

  .skeleton-title {
    width: 60%;
    height: 14px;
    background: #f0ebe3;
    border-radius: 4px;
    margin-bottom: 10px;
    animation: pulse 1.5s ease-in-out infinite;
  }

  .skeleton-message {
    width: 80%;
    height: 12px;
    background: #f0ebe3;
    border-radius: 4px;
    animation: pulse 1.5s ease-in-out infinite 0.2s;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.5; }
  }

  /* Empty State */
  .empty-state {
    text-align: center;
    padding: 48px 20px;
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 20px;
  }

  .empty-icon {
    width: 64px;
    height: 64px;
    margin: 0 auto 16px;
    background: #f0ebe3;
    border-radius: 32px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #b8ab9a;
  }

  .empty-state h3 {
    font-size: 16px;
    font-weight: 600;
    color: #2c2418;
    margin-bottom: 6px;
  }

  .empty-state p {
    font-size: 13px;
    color: #b8ab9a;
  }

  /* Notifications List */
  .notifications-list {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .notification-card {
    display: flex;
    gap: 14px;
    padding: 16px;
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 16px;
    transition: all 0.2s;
  }

  .notification-card:hover {
    border-color: #ddd6cc;
    background: #ffffff;
  }

  .notification-card.unread {
    border-left: 3px solid;
  }

  .notification-card.success.unread { border-left-color: #10b981; }
  .notification-card.warning.unread { border-left-color: #f59e0b; }
  .notification-card.prize.unread { border-left-color: #8b5cf6; }
  .notification-card.transaction.unread { border-left-color: #3b82f6; }
  .notification-card.info.unread { border-left-color: #06b6d4; }

  .notification-icon {
    width: 40px;
    height: 40px;
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
  }

  .notification-card.success .notification-icon { background: #d1fae5; color: #10b981; }
  .notification-card.warning .notification-icon { background: #fef3c7; color: #f59e0b; }
  .notification-card.prize .notification-icon { background: #ede9fe; color: #8b5cf6; }
  .notification-card.transaction .notification-icon { background: #dbeafe; color: #3b82f6; }
  .notification-card.info .notification-icon { background: #cffafe; color: #06b6d4; }

  .notification-content {
    flex: 1;
    min-width: 0;
  }

  .notification-header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 12px;
    margin-bottom: 6px;
  }

  .notification-header h3 {
    font-size: 14px;
    font-weight: 600;
    color: #2c2418;
    margin: 0;
  }

  .notification-card.read .notification-header h3 {
    color: #9b8f7e;
  }

  .notification-meta {
    display: flex;
    align-items: center;
    gap: 8px;
    flex-shrink: 0;
  }

  .notification-time {
    display: flex;
    align-items: center;
    gap: 4px;
    font-size: 10px;
    color: #b8ab9a;
  }

  .delete-btn {
    padding: 4px;
    border-radius: 6px;
    background: none;
    border: none;
    color: #b8ab9a;
    cursor: pointer;
    transition: all 0.2s;
    display: flex;
    align-items: center;
    justify-content: center;
    opacity: 0;
  }

  .notification-card:hover .delete-btn {
    opacity: 1;
  }

  .delete-btn:hover {
    background: #f0ebe3;
    color: #ef4444;
  }

  .notification-message {
    font-size: 12px;
    color: #6b5f4e;
    margin: 0 0 10px;
    line-height: 1.4;
  }

  .notification-card.read .notification-message {
    color: #b8ab9a;
  }

  .notification-footer {
    display: flex;
    align-items: center;
    gap: 16px;
  }

  .action-link {
    font-size: 11px;
    font-weight: 500;
    color: #f59e0b;
    text-decoration: none;
    transition: color 0.2s;
  }

  .action-link:hover {
    color: #ea580c;
    text-decoration: underline;
  }

  .mark-read-link {
    font-size: 11px;
    font-weight: 500;
    color: #9b8f7e;
    background: none;
    border: none;
    cursor: pointer;
    transition: color 0.2s;
  }

  .mark-read-link:hover {
    color: #4a3e2e;
  }

  @media (max-width: 480px) {
    .content {
      padding: 16px;
    }

    .topbar-inner {
      padding: 10px 16px;
    }

    .header-left {
      gap: 8px;
    }

    .logo-area h1 {
      font-size: 14px;
    }

    .mark-read-btn span {
      display: none;
    }

    .mark-read-btn {
      padding: 8px;
    }

    .filter-tabs {
      width: 100%;
    }

    .tab-btn {
      flex: 1;
      justify-content: center;
      padding: 8px 12px;
    }

    .notification-card {
      padding: 14px;
    }

    .notification-header h3 {
      font-size: 13px;
    }

    .notification-message {
      font-size: 11px;
    }
  }
</style>