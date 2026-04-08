<script lang="ts">
  import { BanknoteArrowDown, BanknoteArrowUp, CreditCard, Wallet as WalletIcon, Building2, Clock, CheckCircle, AlertCircle, X, Shield, History, QrCode, ArrowLeft } from 'lucide-svelte';
  import { goto } from '$app/navigation';
  import { page } from '$app/stores';
  
  // Get mode from URL path
  let activeMode: 'deposit' | 'withdraw' = $derived(
    $page.url.pathname.includes('/withdraw') ? 'withdraw' : 'deposit'
  );
  
  let amount: string = $state('');
  let isLoading: boolean = $state(false);
  let showSuccess: boolean = $state(false);
  let showError: boolean = $state(false);
  let errorMessage: string = $state('');
  let transactionId: string = $state('');
  let selectedPaymentMethod: string = $state('card');
  
  // Withdrawal bank details
  let bankName: string = $state('');
  let accountNumber: string = $state('');
  let accountName: string = $state('');
  
  // Available balance
  let balance: number = $state(2500000);
  
  // Quick amount options - responsive sizing
  const quickAmounts = [100, 500, 1000, 5000, 10000];
  
  // Payment methods
  const paymentMethods = [
    { id: 'card', name: 'Card', icon: CreditCard, description: 'Credit / Debit' },
    { id: 'wallet', name: 'Wallet', icon: WalletIcon, description: 'Digital Wallet' },
    { id: 'bank', name: 'Bank', icon: Building2, description: 'Bank Transfer' },
    { id: 'crypto', name: 'Crypto', icon: QrCode, description: 'USDT / BTC' },
  ];
  
  const modeConfig = {
    deposit: {
      icon: BanknoteArrowUp,
      iconColor: 'text-emerald-500',
      bgGradient: 'from-emerald-500 to-teal-600',
      buttonText: 'Deposit',
      minAmount: 5,
      color: 'emerald',
      path: '/wallet/deposit'
    },
    withdraw: {
      icon: BanknoteArrowDown,
      iconColor: 'text-rose-500',
      bgGradient: 'from-rose-500 to-pink-600',
      buttonText: 'Withdraw',
      minAmount: 10,
      color: 'rose',
      path: '/wallet/withdraw'
    }
  };
  
  const config = $derived(modeConfig[activeMode]);
  
  function validateAmount(): boolean {
    const numAmount = parseFloat(amount);
    if (isNaN(numAmount) || numAmount <= 0) {
      errorMessage = 'Enter a valid amount';
      showError = true;
      setTimeout(() => { showError = false; }, 3000);
      return false;
    }
    
    if (activeMode === 'withdraw' && numAmount > balance) {
      errorMessage = `Insufficient balance. Available: $${balance.toLocaleString()}`;
      showError = true;
      setTimeout(() => { showError = false; }, 3000);
      return false;
    }
    
    if (numAmount < config.minAmount) {
      errorMessage = `Minimum ${activeMode} is $${config.minAmount}`;
      showError = true;
      setTimeout(() => { showError = false; }, 3000);
      return false;
    }
    
    return true;
  }
  
  function validateWithdrawalDetails(): boolean {
    if (activeMode === 'withdraw') {
      if (!bankName.trim()) {
        errorMessage = 'Enter your bank name';
        showError = true;
        setTimeout(() => { showError = false; }, 3000);
        return false;
      }
      if (!accountNumber.trim()) {
        errorMessage = 'Enter your account number';
        showError = true;
        setTimeout(() => { showError = false; }, 3000);
        return false;
      }
      if (!accountName.trim()) {
        errorMessage = 'Enter account holder name';
        showError = true;
        setTimeout(() => { showError = false; }, 3000);
        return false;
      }
    }
    return true;
  }
  
  async function handleSubmit() {
    if (!validateAmount()) return;
    if (!validateWithdrawalDetails()) return;
    
    isLoading = true;
    await new Promise(resolve => setTimeout(resolve, 1500));
    
    const numAmount = parseFloat(amount);
    
    if (activeMode === 'deposit') {
      balance += numAmount;
      transactionId = 'DEP_' + Math.random().toString(36).substr(2, 9).toUpperCase();
    } else {
      balance -= numAmount;
      transactionId = 'WDL_' + Math.random().toString(36).substr(2, 9).toUpperCase();
    }
    
    isLoading = false;
    showSuccess = true;
    
    setTimeout(() => {
      showSuccess = false;
      resetForm();
    }, 2500);
  }
  
  function resetForm() {
    amount = '';
    bankName = '';
    accountNumber = '';
    accountName = '';
    selectedPaymentMethod = 'card';
  }
  
  function setQuickAmount(value: number) {
    amount = value.toString();
    errorMessage = '';
    showError = false;
  }
  
  function switchMode(mode: 'deposit' | 'withdraw') {
    goto(modeConfig[mode].path);
  }
</script>

<div class="min-h-screen bg-gradient-to-br from-gray-50 to-gray-100 pb-[6rem]">
  <!-- Back Button - Responsive Floating -->
  <button 
    onclick={() => goto('/')}
    class="fixed top-4 left-4 z-20 p-2 sm:p-2.5 bg-white/80 backdrop-blur-sm rounded-full shadow-md hover:bg-white transition-all active:scale-95 hidden md:block"
  >
    <ArrowLeft size={18} class="sm:w-5 sm:h-5 text-gray-600" />
  </button>

  <!-- Main Container - Responsive Padding -->
  <div class="w-full px-3 sm:px-4 md:px-6 py-4 sm:py-6 md:py-8">
    <div class="max-w-md mx-auto">
      <!-- Balance Card - Responsive Sizing -->
      <div class="mb-6 sm:mb-8 text-center">
        <div class="relative inline-block w-full">
          <div class="absolute inset-0 bg-gradient-to-r from-indigo-500 to-purple-500 rounded-2xl sm:rounded-3xl blur-2xl opacity-30"></div>
          <div class="relative bg-gradient-to-br from-indigo-500 via-purple-500 to-pink-500 rounded-2xl sm:rounded-3xl p-6 sm:p-8 shadow-2xl">
            <div class="absolute top-0 right-0 w-24 sm:w-32 h-24 sm:h-32 bg-white/5 rounded-full -mr-12 sm:-mr-16 -mt-12 sm:-mt-16"></div>
            <div class="absolute bottom-0 left-0 w-20 sm:w-24 h-20 sm:h-24 bg-white/5 rounded-full -ml-10 sm:-ml-12 -mb-10 sm:-mb-12"></div>
            <div class="relative">
              <div class="flex items-center justify-center gap-1.5 sm:gap-2 mb-1 sm:mb-2">
                <Shield size={12} class="sm:w-3.5 sm:h-3.5 text-white/60" />
                <span class="text-[10px] sm:text-xs font-medium text-white/60 uppercase tracking-wide">Total Balance</span>
              </div>
              <div class="text-3xl sm:text-4xl md:text-5xl font-bold text-white mb-1 sm:mb-2 tracking-tight">
                ${balance.toLocaleString()}
              </div>
              <div class="flex items-center justify-center gap-1.5 sm:gap-2">
                <div class="w-1 h-1 bg-white/40 rounded-full"></div>
                <span class="text-[10px] sm:text-xs text-white/50">Available for use</span>
                <div class="w-1 h-1 bg-white/40 rounded-full"></div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Mode Toggle - Responsive (Now syncs with URL) -->
      <div class="mb-6 sm:mb-8">
        <div class="bg-gray-100/80 backdrop-blur-sm rounded-xl sm:rounded-2xl p-1">
          <div class="grid grid-cols-2 gap-1">
            <button
              onclick={() => switchMode('deposit')}
              class="relative py-2.5 sm:py-3 rounded-lg sm:rounded-xl transition-all duration-300"
              class:bg-white={activeMode === 'deposit'}
              class:shadow-sm={activeMode === 'deposit'}
            >
              <div class="flex items-center justify-center gap-1.5 sm:gap-2">
                <BanknoteArrowUp size={16} class="sm:w-4.5 sm:h-4.5 {activeMode === 'deposit' ? 'text-emerald-500' : 'text-gray-400'}" />
                <span class="font-semibold text-xs sm:text-sm {activeMode === 'deposit' ? 'text-gray-900' : 'text-gray-500'}">
                  Deposit
                </span>
              </div>
            </button>
            
            <button
              onclick={() => switchMode('withdraw')}
              class="relative py-2.5 sm:py-3 rounded-lg sm:rounded-xl transition-all duration-300"
              class:bg-white={activeMode === 'withdraw'}
              class:shadow-sm={activeMode === 'withdraw'}
            >
              <div class="flex items-center justify-center gap-1.5 sm:gap-2">
                <BanknoteArrowDown size={16} class="sm:w-4.5 sm:h-4.5 {activeMode === 'withdraw' ? 'text-rose-500' : 'text-gray-400'}" />
                <span class="font-semibold text-xs sm:text-sm {activeMode === 'withdraw' ? 'text-gray-900' : 'text-gray-500'}">
                  Withdraw
                </span>
              </div>
            </button>
          </div>
        </div>
      </div>

      <!-- Transaction Card - Responsive -->
      <div class="bg-white rounded-xl sm:rounded-2xl shadow-lg border border-gray-100 overflow-hidden">
        <!-- Success Message -->
        {#if showSuccess}
          <div class="m-3 sm:m-4 p-2.5 sm:p-3 bg-emerald-50 border border-emerald-200 rounded-lg sm:rounded-xl flex items-center gap-2 sm:gap-3 animate-slideDown">
            <div class="p-1 bg-emerald-500 rounded-full flex-shrink-0">
              <CheckCircle size={12} class="sm:w-3.5 sm:h-3.5 text-white" />
            </div>
            <div class="flex-1 min-w-0">
              <p class="text-xs sm:text-sm font-semibold text-emerald-800 truncate">Success!</p>
              <p class="text-[10px] sm:text-xs text-emerald-600 mt-0.5 truncate">
                {activeMode === 'deposit' ? 'Deposited' : 'Withdrew'} ${parseFloat(amount).toLocaleString()}
              </p>
            </div>
            <button onclick={() => showSuccess = false} class="text-emerald-500 flex-shrink-0">
              <X size={14} class="sm:w-4 sm:h-4" />
            </button>
          </div>
        {/if}
        
        <!-- Error Message -->
        {#if showError}
          <div class="m-3 sm:m-4 p-2.5 sm:p-3 bg-rose-50 border border-rose-200 rounded-lg sm:rounded-xl flex items-center gap-2 sm:gap-3 animate-slideDown">
            <div class="p-1 bg-rose-500 rounded-full flex-shrink-0">
              <AlertCircle size={12} class="sm:w-3.5 sm:h-3.5 text-white" />
            </div>
            <div class="flex-1 min-w-0">
              <p class="text-xs sm:text-sm font-semibold text-rose-800 truncate">Error</p>
              <p class="text-[10px] sm:text-xs text-rose-600 mt-0.5 truncate">{errorMessage}</p>
            </div>
            <button onclick={() => showError = false} class="text-rose-500 flex-shrink-0">
              <X size={14} class="sm:w-4 sm:h-4" />
            </button>
          </div>
        {/if}
        
        <form preventDefault={handleSubmit} class="p-4 sm:p-5 space-y-4 sm:space-y-5">
          <!-- Amount Input -->
          <div>
            <label class="block text-[10px] sm:text-xs font-semibold text-gray-500 uppercase tracking-wide mb-1.5 sm:mb-2">
              Enter Amount
            </label>
            <div class="relative">
              <span class="absolute left-3 sm:left-4 top-1/2 -translate-y-1/2 text-xl sm:text-2xl text-gray-400 font-light">$</span>
              <input
                type="number"
                bind:value={amount}
                placeholder="0"
                step="0.01"
                min={config.minAmount}
                class="w-full pl-8 sm:pl-10 pr-3 sm:pr-4 py-3 sm:py-4 text-xl sm:text-2xl font-semibold border-2 border-gray-100 rounded-lg sm:rounded-xl focus:ring-2 focus:ring-{config.color}-500 focus:border-{config.color}-500 outline-none transition bg-gray-50/50"
                class:border-rose-200={showError}
              />
            </div>
            <p class="text-[10px] sm:text-xs text-gray-400 mt-1.5 text-center">
              Minimum: ${config.minAmount}
            </p>
          </div>
          
          <!-- Quick Amounts - Responsive Grid -->
          <div>
            <p class="text-[10px] sm:text-xs font-medium text-gray-500 mb-2 text-center">Quick Select</p>
            <div class="grid grid-cols-3 sm:grid-cols-5 gap-1.5 sm:gap-2">
              {#each quickAmounts as qa}
                <button
                  type="button"
                  onclick={() => setQuickAmount(qa)}
                  class="px-2 sm:px-4 py-1.5 sm:py-2 text-xs sm:text-sm font-medium rounded-lg sm:rounded-xl transition-all {parseFloat(amount) === qa ? 'bg-indigo-600 text-white shadow-md' : 'bg-gray-100 text-gray-700 hover:bg-gray-200'}"
                >
                  ${qa}
                </button>
              {/each}
            </div>
          </div>
          
          <!-- Payment Method (Deposit only) - Responsive Grid -->
          {#if activeMode === 'deposit'}
            <div>
              <p class="text-[10px] sm:text-xs font-semibold text-gray-500 uppercase tracking-wide mb-2 text-center">
                Payment Method
              </p>
              <div class="grid grid-cols-2 sm:grid-cols-4 gap-2 sm:gap-2">
                {#each paymentMethods as method}
                  <button
                    type="button"
                    onclick={() => selectedPaymentMethod = method.id}
                    class="p-2 sm:p-2.5 rounded-lg sm:rounded-xl text-center transition-all {selectedPaymentMethod === method.id ? 'bg-indigo-50 border-2 border-indigo-200' : 'bg-gray-50 border border-gray-100'}"
                  >
                    <method.icon size={18} class="sm:w-5 sm:h-5 {selectedPaymentMethod === method.id ? 'text-indigo-600 mx-auto' : 'text-gray-400 mx-auto'}" />
                    <p class="text-[10px] sm:text-xs font-medium mt-1 {selectedPaymentMethod === method.id ? 'text-indigo-600' : 'text-gray-500'}">{method.name}</p>
                  </button>
                {/each}
              </div>
            </div>
          {/if}
          
          <!-- Bank Details (Withdrawal only) - Responsive -->
          {#if activeMode === 'withdraw'}
            <div class="space-y-3">
              <div class="text-center mb-1 sm:mb-2">
                <p class="text-[10px] sm:text-xs font-semibold text-gray-500 uppercase tracking-wide">Bank Account</p>
              </div>
              
              <div>
                <input
                  type="text"
                  bind:value={bankName}
                  placeholder="Bank name"
                  class="w-full px-3 sm:px-4 py-2 sm:py-2.5 text-xs sm:text-sm border border-gray-200 rounded-lg sm:rounded-xl focus:ring-2 focus:ring-rose-500 focus:border-rose-500 outline-none transition bg-gray-50/50"
                />
              </div>
              
              <div>
                <input
                  type="text"
                  bind:value={accountNumber}
                  placeholder="Account number"
                  class="w-full px-3 sm:px-4 py-2 sm:py-2.5 text-xs sm:text-sm border border-gray-200 rounded-lg sm:rounded-xl focus:ring-2 focus:ring-rose-500 focus:border-rose-500 outline-none transition bg-gray-50/50"
                />
              </div>
              
              <div>
                <input
                  type="text"
                  bind:value={accountName}
                  placeholder="Account holder name"
                  class="w-full px-3 sm:px-4 py-2 sm:py-2.5 text-xs sm:text-sm border border-gray-200 rounded-lg sm:rounded-xl focus:ring-2 focus:ring-rose-500 focus:border-rose-500 outline-none transition bg-gray-50/50"
                />
              </div>
            </div>
          {/if}
          
          <!-- Submit Button - Responsive -->
          <button
            type="submit"
            disabled={isLoading}
            class="w-full bg-gradient-to-r {config.bgGradient} text-white py-3 sm:py-3.5 rounded-lg sm:rounded-xl font-bold text-sm sm:text-base transition-all hover:shadow-xl hover:-translate-y-0.5 active:translate-y-0 disabled:opacity-50 disabled:cursor-not-allowed"
          >
            {#if isLoading}
              <span class="flex items-center justify-center gap-2">
                <div class="w-3.5 h-3.5 sm:w-4 sm:h-4 border-2 border-white border-t-transparent rounded-full animate-spin"></div>
                <span class="text-xs sm:text-sm">Processing...</span>
              </span>
            {:else}
              <span class="flex items-center justify-center gap-1.5 sm:gap-2">
                <config.icon size={16} class="sm:w-4.5 sm:h-4.5" />
                {config.buttonText}
              </span>
            {/if}
          </button>
          
          <!-- Info Note - Responsive -->
          <div class="flex items-center justify-center gap-1.5 sm:gap-2 pt-1 sm:pt-2">
            <Clock size={10} class="sm:w-3 sm:h-3 text-gray-400" />
            <p class="text-[9px] sm:text-[10px] text-gray-400 text-center">
              {#if activeMode === 'deposit'}
                Instant • Secure • No fees
              {:else}
                1-3 business days • Secure transfer
              {/if}
            </p>
          </div>
          
          <!-- Transaction History Link - Responsive -->
          <div class="text-center pt-1 border-t border-gray-100 mt-2">
            <a href="/wallet/history" class="inline-flex items-center gap-1 sm:gap-1.5 text-[10px] sm:text-xs text-gray-400 hover:text-indigo-600 transition-colors">
              <History size={10} class="sm:w-3 sm:h-3" />
              Transaction History
            </a>
          </div>
        </form>
      </div>
    </div>
  </div>
</div>

<style>
  @keyframes slideDown {
    from {
      opacity: 0;
      transform: translateY(-10px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
  
  .animate-slideDown {
    animation: slideDown 0.2s ease-out;
  }
  
  /* Responsive touch targets for mobile */
  @media (max-width: 640px) {
    button, 
    [type="button"],
    [type="submit"] {
      min-height: 44px;
    }
    
    input {
      font-size: 16px !important; /* Prevents zoom on iOS */
    }
  }
  
  /* Smooth scrolling */
  html {
    scroll-behavior: smooth;
  }
</style>