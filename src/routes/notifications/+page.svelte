<script lang="ts">
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';
  import { 
    Bell, 
    ArrowLeft, 
    CheckCircle, 
    AlertCircle, 
    Info, 
    Trophy, 
    Wallet, 
    CheckCheck,
    Trash2,
    Clock,
    X
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
      case 'success': return 'text-emerald-500';
      case 'warning': return 'text-amber-500';
      case 'prize': return 'text-purple-500';
      case 'transaction': return 'text-blue-500';
      default: return 'text-cyan-500';
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

<div class="min-h-screen bg-gray-50">
  <!-- Header -->
  <div class="sticky top-0 z-20 bg-white border-b border-gray-100">
    <div class="max-w-2xl mx-auto px-4 py-3">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-3">
          <button 
            onclick={() => goto('/')}
            class="p-1.5 -ml-1.5 hover:bg-gray-100 rounded-full transition-colors"
          >
            <ArrowLeft size={20} class="text-gray-600" />
          </button>
          <div class="flex items-center gap-2">
            <Bell size={18} class="text-gray-600" />
            <h1 class="text-lg font-semibold text-gray-900">Notifications</h1>
            {#if unreadCount > 0}
              <span class="text-xs text-indigo-600 bg-indigo-50 px-1.5 py-0.5 rounded-full">
                {unreadCount} new
              </span>
            {/if}
          </div>
        </div>
        
        <div class="flex items-center gap-2">
          {#if unreadCount > 0}
            <button
              onclick={markAllAsRead}
              class="p-1.5 hover:bg-gray-100 rounded-full transition-colors"
              title="Mark all as read"
            >
              <CheckCheck size={18} class="text-gray-500" />
            </button>
          {/if}
        </div>
      </div>
    </div>
  </div>
  
  <div class="max-w-2xl mx-auto px-4 py-4">
    <!-- Filter Toggle -->
    <div class="mb-4">
      <div class="inline-flex bg-gray-100 rounded-lg p-0.5">
        <button
          onclick={() => filter = 'all'}
          class="px-4 py-1.5 text-sm font-medium rounded-md transition-all {filter === 'all' ? 'bg-white text-gray-900 shadow-sm' : 'text-gray-500 hover:text-gray-700'}"
        >
          All
        </button>
        <button
          onclick={() => filter = 'unread'}
          class="px-4 py-1.5 text-sm font-medium rounded-md transition-all {filter === 'unread' ? 'bg-white text-gray-900 shadow-sm' : 'text-gray-500 hover:text-gray-700'}"
        >
          Unread
          {#if unreadCount > 0}
            <span class="ml-1 text-xs text-indigo-600">{unreadCount}</span>
          {/if}
        </button>
      </div>
    </div>
    
    <!-- Loading State -->
    {#if isLoading}
      <div class="space-y-2">
        {#each [1, 2, 3] as i}
          <div class="bg-white rounded-lg p-4 animate-pulse">
            <div class="flex gap-3">
              <div class="w-8 h-8 bg-gray-200 rounded-full"></div>
              <div class="flex-1">
                <div class="h-4 bg-gray-200 rounded w-1/3 mb-2"></div>
                <div class="h-3 bg-gray-200 rounded w-2/3"></div>
              </div>
            </div>
          </div>
        {/each}
      </div>
    {:else if filteredNotifications.length === 0}
      <!-- Empty State -->
      <div class="text-center py-12">
        <div class="inline-flex p-3 bg-gray-100 rounded-full mb-3">
          <Bell size={24} class="text-gray-400" />
        </div>
        <p class="text-sm text-gray-500">
          {filter === 'unread' ? 'No unread notifications' : 'No notifications yet'}
        </p>
      </div>
    {:else}
      <!-- Notifications List -->
      <div class="space-y-1">
        {#each filteredNotifications as notification (notification.id)}
          {@const Icon = getTypeIcon(notification.type)}
          {@const iconColor = getTypeColor(notification.type)}
          
          <div class="bg-white rounded-lg transition-all {notification.read ? 'opacity-70' : ''}">
            <div class="p-4">
              <div class="flex gap-3">
                <!-- Icon -->
                <div class="flex-shrink-0 mt-0.5">
                  <Icon size={18} class={iconColor} />
                </div>
                
                <!-- Content -->
                <div class="flex-1 min-w-0">
                  <div class="flex items-start justify-between gap-2 mb-1">
                    <h3 class={`text-sm font-medium ${notification.read ? 'text-gray-600' : 'text-gray-900'}`}>
                      {notification.title}
                    </h3>
                    <div class="flex items-center gap-2 flex-shrink-0">
                      <span class="text-xs text-gray-400">
                        {formatTimestamp(notification.timestamp)}
                      </span>
                      <button
                        onclick={() => deleteNotification(notification.id)}
                        class="opacity-0 group-hover:opacity-100 p-1 hover:bg-gray-100 rounded-full transition-all"
                        title="Delete"
                      >
                        <X size={12} class="text-gray-400" />
                      </button>
                    </div>
                  </div>
                  
                  <p class="text-xs text-gray-500 mb-2">
                    {notification.message}
                  </p>
                  
                  <div class="flex items-center gap-3">
                    {#if notification.actionUrl}
                      <a
                        href={notification.actionUrl}
                        onclick={() => markAsRead(notification.id)}
                        class="text-xs text-indigo-600 hover:text-indigo-700 transition-colors"
                      >
                        View →
                      </a>
                    {/if}
                    
                    {#if !notification.read}
                      <button
                        onclick={() => markAsRead(notification.id)}
                        class="text-xs text-gray-400 hover:text-gray-600 transition-colors"
                      >
                        Mark read
                      </button>
                    {/if}
                  </div>
                </div>
              </div>
            </div>
          </div>
        {/each}
      </div>
    {/if}
  </div>
</div>

<style>
  /* Group hover for delete button */
  .group-hover\:opacity-100 {
    opacity: 0;
  }
  
  div:has(> .group-hover\:opacity-100):hover button {
    opacity: 1;
  }
</style>