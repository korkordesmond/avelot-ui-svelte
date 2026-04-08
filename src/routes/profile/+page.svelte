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
    Check
  } from 'lucide-svelte';
  
  // User data
  let user = $state({
    name: 'Jane Smith',
    email: 'jane.smith@example.com',
    phone: '+1 (555) 123-4567',
    memberSince: 'January 2024',
    location: 'New York, USA',
    avatar: null as string | null,
    verified: true
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

<div class="min-h-screen bg-gray-50">
  <!-- Header -->
  <div class="sticky top-0 z-20 bg-white border-b border-gray-100">
    <div class="max-w-2xl mx-auto px-4 py-3">
      <div class="flex items-center gap-3">
        <button 
          onclick={() => goto('/')}
          class="p-1.5 -ml-1.5 hover:bg-gray-100 rounded-full transition-colors"
        >
          <ArrowLeft size={20} class="text-gray-600" />
        </button>
        <h1 class="text-lg font-semibold text-gray-900">Profile</h1>
      </div>
    </div>
  </div>
  
  <div class="max-w-2xl mx-auto px-4 py-6">
    <!-- Profile Header -->
    <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-6 mb-4">
      <div class="flex flex-col sm:flex-row items-center sm:items-start gap-4">
        <!-- Avatar -->
        <div class="relative">
          <div class="w-24 h-24 rounded-full bg-gradient-to-br from-indigo-500 to-purple-600 flex items-center justify-center">
            <span class="text-3xl font-bold text-white">
              {user.name.charAt(0)}
            </span>
          </div>
        </div>
        
        <!-- User Info -->
        <div class="flex-1 text-center sm:text-left">
          <div class="flex items-center justify-center sm:justify-start gap-2 mb-1">
            <h2 class="text-xl font-bold text-gray-900">{user.name}</h2>
            {#if user.verified}
              <div class="inline-flex items-center gap-1 px-1.5 py-0.5 bg-emerald-50 rounded-full">
                <BadgeCheck size={12} class="text-emerald-500" />
                <span class="text-[10px] font-medium text-emerald-600">Verified</span>
              </div>
            {/if}
          </div>
          
          <div class="flex flex-col sm:flex-row items-center justify-center sm:justify-start gap-2 text-sm text-gray-500 mb-3">
            <div class="flex items-center gap-1">
              <Mail size={12} />
              <span>{user.email}</span>
              <button 
                onclick={() => copyToClipboard(user.email)}
                class="p-0.5 hover:bg-gray-100 rounded transition-colors"
              >
                {#if copiedEmail}
                  <Check size={12} class="text-emerald-500" />
                {:else}
                  <Copy size={12} class="text-gray-400" />
                {/if}
              </button>
            </div>
            <span class="hidden sm:inline text-gray-300">•</span>
            <div class="flex items-center gap-1">
              <Phone size={12} />
              <span>{user.phone}</span>
            </div>
          </div>
          
          <!-- Stats -->
          <div class="flex flex-wrap gap-4 justify-center sm:justify-start">
            <div class="flex items-center gap-1.5 text-xs text-gray-500">
              <Calendar size={12} />
              <span>Joined {user.memberSince}</span>
            </div>
            <div class="flex items-center gap-1.5 text-xs text-gray-500">
              <MapPin size={12} />
              <span>{user.location}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Payment Methods -->
    <div class="bg-white rounded-2xl shadow-sm border border-gray-100 mb-4 overflow-hidden">
      <div class="flex items-center justify-between p-5 border-b border-gray-100">
        <div class="flex items-center gap-2">
          <CreditCard size={18} class="text-gray-600" />
          <h3 class="font-semibold text-gray-900">Payment Methods</h3>
        </div>
        <button class="text-xs font-medium text-indigo-600 hover:text-indigo-700">
          + Add New
        </button>
      </div>
      
      <div class="divide-y divide-gray-100">
        {#each paymentMethods as method}
          <div class="p-4 hover:bg-gray-50 transition-colors">
            <div class="flex items-center justify-between">
              <div class="flex items-center gap-3">
                <div class="w-10 h-10 bg-gray-100 rounded-xl flex items-center justify-center">
                  <CreditCard size={18} class="text-gray-600" />
                </div>
                <div>
                  <p class="text-sm font-medium text-gray-900">
                    {method.type} •••• {method.last4}
                  </p>
                  <p class="text-xs text-gray-500">Expires {method.expiry}</p>
                </div>
              </div>
              <div class="flex items-center gap-2">
                {#if method.isDefault}
                  <span class="text-xs text-emerald-600 bg-emerald-50 px-2 py-0.5 rounded-full">Default</span>
                {/if}
                <button 
                  onclick={() => removePaymentMethod(method.id)}
                  class="text-xs text-gray-400 hover:text-red-500 transition-colors"
                >
                  Remove
                </button>
              </div>
            </div>
          </div>
        {/each}
      </div>
    </div>
    
    <!-- Settings Sections -->
    <div class="bg-white rounded-2xl shadow-sm border border-gray-100 mb-4 overflow-hidden">
      <button class="w-full flex items-center justify-between p-5 hover:bg-gray-50 transition-colors border-b border-gray-100">
        <div class="flex items-center gap-3">
          <div class="w-8 h-8 bg-gray-100 rounded-xl flex items-center justify-center">
            <Shield size={16} class="text-gray-600" />
          </div>
          <span class="font-medium text-gray-900">Security</span>
        </div>
        <ChevronRight size={14} class="text-gray-400" />
      </button>
      
      <button class="w-full flex items-center justify-between p-5 hover:bg-gray-50 transition-colors border-b border-gray-100">
        <div class="flex items-center gap-3">
          <div class="w-8 h-8 bg-gray-100 rounded-xl flex items-center justify-center">
            <Smartphone size={16} class="text-gray-600" />
          </div>
          <span class="font-medium text-gray-900">Linked Devices</span>
        </div>
        <ChevronRight size={14} class="text-gray-400" />
      </button>
      
      <button class="w-full flex items-center justify-between p-5 hover:bg-gray-50 transition-colors border-b border-gray-100">
        <div class="flex items-center gap-3">
          <div class="w-8 h-8 bg-gray-100 rounded-xl flex items-center justify-center">
            <Headphones size={16} class="text-gray-600" />
          </div>
          <span class="font-medium text-gray-900">Support</span>
        </div>
        <ChevronRight size={14} class="text-gray-400" />
      </button>
      
      <button class="w-full flex items-center justify-between p-5 hover:bg-gray-50 transition-colors">
        <div class="flex items-center gap-3">
          <div class="w-8 h-8 bg-gray-100 rounded-xl flex items-center justify-center">
            <Info size={16} class="text-gray-600" />
          </div>
          <span class="font-medium text-gray-900">About Us</span>
        </div>
        <ChevronRight size={14} class="text-gray-400" />
      </button>
    </div>
    
    <!-- Logout Button -->
    <div class="mt-6">
      {#if showLogoutConfirm}
        <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-5 mb-4">
          <p class="text-sm text-gray-700 mb-4">Are you sure you want to logout?</p>
          <div class="flex gap-3">
            <button
              onclick={handleLogout}
              class="flex-1 px-4 py-2 bg-red-600 text-white text-sm font-medium rounded-lg hover:bg-red-700 transition-colors"
            >
              Logout
            </button>
            <button
              onclick={() => showLogoutConfirm = false}
              class="flex-1 px-4 py-2 bg-gray-100 text-gray-700 text-sm font-medium rounded-lg hover:bg-gray-200 transition-colors"
            >
              Cancel
            </button>
          </div>
        </div>
      {/if}
      
      <button
        onclick={() => showLogoutConfirm = true}
        class="w-full flex items-center justify-center gap-2 px-4 py-3 bg-white border border-gray-200 rounded-xl text-red-600 font-medium hover:bg-red-50 hover:border-red-200 transition-all"
      >
        <LogOut size={18} />
        Logout
      </button>
    </div>
  </div>
</div>