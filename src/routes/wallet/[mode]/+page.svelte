<script lang="ts">
  import { CreditCard, Wallet as WalletIcon, Building2, QrCode, Clock, History, CheckCircle, AlertCircle, X, ArrowDownLeft, ArrowUpRight, TrendingUp, Sparkles } from 'lucide-svelte';
  import { goto } from '$app/navigation';
  import { page } from '$app/stores';

  let activeMode: 'deposit' | 'withdraw' = $derived(
    $page.url.pathname.includes('/withdraw') ? 'withdraw' : 'deposit'
  );

  let amount: string = $state('');
  let isLoading: boolean = $state(false);
  let showSuccess: boolean = $state(false);
  let showError: boolean = $state(false);
  let errorMessage: string = $state('');
  let selectedPaymentMethod: string = $state('card');

  let bankName: string = $state('');
  let accountNumber: string = $state('');
  let accountName: string = $state('');

  let balance: number = $state(2500000);

  const quickAmounts = [100, 500, 1000, 5000, 10000];

  const paymentMethods = [
    { id: 'card',   name: 'Card',   icon: CreditCard  },
    { id: 'wallet', name: 'Wallet', icon: WalletIcon   },
    { id: 'bank',   name: 'Bank',   icon: Building2    },
    { id: 'crypto', name: 'Crypto', icon: QrCode       },
  ];

  const modeConfig = {
    deposit: {
      icon: ArrowDownLeft,
      minAmount: 5,
      path: '/wallet/deposit',
      note: 'Instant · Secure · No fees',
      buttonText: 'Deposit funds',
      accent: 'emerald',
    },
    withdraw: {
      icon: ArrowUpRight,
      minAmount: 10,
      path: '/wallet/withdraw',
      note: '1–3 business days · Secure transfer',
      buttonText: 'Withdraw funds',
      accent: 'rose',
    },
  };

  const config = $derived(modeConfig[activeMode]);

  const isSubmitDisabled = $derived(() => {
    if (isLoading) return true;
    
    const amountNum = parseFloat(amount);
    const isValidAmount = !isNaN(amountNum) && amountNum >= config.minAmount;
    
    if (!isValidAmount) return true;
    
    if (activeMode === 'deposit') return false;
    
    const hasBankDetails = bankName.trim() !== '' && 
                          accountNumber.trim() !== '' && 
                          accountName.trim() !== '';
    return !hasBankDetails;
  });

  function triggerError(msg: string) {
    errorMessage = msg;
    showError = true;
    setTimeout(() => { showError = false; }, 3000);
  }

  function validateAmount(): boolean {
    const n = parseFloat(amount);
    if (isNaN(n) || n <= 0) { triggerError('Enter a valid amount'); return false; }
    if (n < config.minAmount) { triggerError(`Minimum is $${config.minAmount}`); return false; }
    if (activeMode === 'withdraw' && n > balance) {
      triggerError(`Insufficient balance. Available: $${balance.toLocaleString()}`);
      return false;
    }
    return true;
  }

  function validateWithdrawalDetails(): boolean {
    if (activeMode === 'withdraw') {
      if (!bankName.trim())      { triggerError('Enter your bank name'); return false; }
      if (!accountNumber.trim()) { triggerError('Enter your account number'); return false; }
      if (!accountName.trim())   { triggerError('Enter account holder name'); return false; }
    }
    return true;
  }

  async function handleSubmit() {
    if (!validateAmount() || !validateWithdrawalDetails()) return;
    isLoading = true;
    await new Promise(r => setTimeout(r, 1500));
    const n = parseFloat(amount);
    balance += activeMode === 'deposit' ? n : -n;
    isLoading = false;
    showSuccess = true;
    setTimeout(() => { showSuccess = false; resetForm(); }, 2500);
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
    showError = false;
  }

  function switchMode(mode: 'deposit' | 'withdraw') {
    goto(modeConfig[mode].path);
  }
</script>

<div class="page">
  <div class="container">
    
    <!-- Balance Card -->
    <div class="balance-card">
      <div class="balance-header">
        <div class="balance-label">
          <TrendingUp size={13} />
          <span>Available Balance</span>
        </div>
        <div class="balance-chip">
          <Sparkles size={10} />
          <span>Active</span>
        </div>
      </div>
      <div class="balance-amount">${balance.toLocaleString()}</div>
      <div class="balance-sub">USD Account · Instant settlement</div>
    </div>

    <!-- Mode Toggle -->
    <div class="mode-toggle">
      <button
        onclick={() => switchMode('deposit')}
        class="mode-btn {activeMode === 'deposit' ? 'active-deposit' : ''}"
      >
        <ArrowDownLeft size={16} />
        Deposit
      </button>
      <button
        onclick={() => switchMode('withdraw')}
        class="mode-btn {activeMode === 'withdraw' ? 'active-withdraw' : ''}"
      >
        <ArrowUpRight size={16} />
        Withdraw
      </button>
    </div>

    <!-- Main Card -->
    <div class="action-card">
      
      <!-- Toasts -->
      {#if showSuccess}
        <div class="toast success">
          <CheckCircle size={16} />
          <span>{activeMode === 'deposit' ? 'Deposited' : 'Withdrew'} ${parseFloat(amount || '0').toLocaleString()} successfully</span>
          <button onclick={() => showSuccess = false}><X size={12} /></button>
        </div>
      {/if}
      {#if showError}
        <div class="toast error">
          <AlertCircle size={16} />
          <span>{errorMessage}</span>
          <button onclick={() => showError = false}><X size={12} /></button>
        </div>
      {/if}

      <form onsubmit={(e) => { e.preventDefault(); handleSubmit(); }}>

        <!-- Amount Input -->
        <div class="field">
          <label class="field-label">Amount</label>
          <div class="amount-wrapper">
            <span class="currency-symbol">$</span>
            <input
              type="number"
              bind:value={amount}
              placeholder="0.00"
              step="0.01"
              min={config.minAmount}
              class="amount-input {showError ? 'error' : ''} {activeMode}"
            />
          </div>
          <p class="field-hint">Minimum ${config.minAmount}</p>
        </div>

        <!-- Quick Amounts -->
        <div class="field">
          <label class="field-label">Quick select</label>
          <div class="quick-grid">
            {#each quickAmounts as qa}
              <button
                type="button"
                onclick={() => setQuickAmount(qa)}
                class="quick-btn {parseFloat(amount) === qa ? (activeMode === 'deposit' ? 'active-deposit' : 'active-withdraw') : ''}"
              >
                ${qa.toLocaleString()}
              </button>
            {/each}
          </div>
        </div>

        <!-- Payment Methods (Deposit only) -->
        {#if activeMode === 'deposit'}
          <div class="field">
            <label class="field-label">Payment method</label>
            <div class="methods-grid">
              {#each paymentMethods as m}
                <button
                  type="button"
                  onclick={() => selectedPaymentMethod = m.id}
                  class="method-btn {selectedPaymentMethod === m.id ? 'active' : ''}"
                >
                  <m.icon size={18} />
                  <span>{m.name}</span>
                </button>
              {/each}
            </div>
          </div>
        {/if}

        <!-- Bank Details (Withdraw only) -->
        {#if activeMode === 'withdraw'}
          <div class="field">
            <label class="field-label">Bank details</label>
            <div class="bank-fields">
              <input type="text" bind:value={bankName} placeholder="Bank name" class="bank-input" />
              <input type="text" bind:value={accountNumber} placeholder="Account number" class="bank-input" />
              <input type="text" bind:value={accountName} placeholder="Account holder name" class="bank-input" />
            </div>
          </div>
        {/if}

        <!-- Submit Button -->
        <button
          type="submit"
          disabled={isSubmitDisabled()}
          class="submit-btn {activeMode}"
        >
          {#if isLoading}
            <div class="spinner" />
            <span>Processing…</span>
          {:else}
            <config.icon size={16} />
            <span>{config.buttonText}</span>
          {/if}
        </button>

        <!-- Validation Hint -->
        {#if !isSubmitDisabled() && !isLoading}
          <p class="hint-success">✓ Ready to {activeMode === 'deposit' ? 'deposit' : 'withdraw'}</p>
        {:else if !isLoading && activeMode === 'withdraw' && parseFloat(amount) >= config.minAmount && (!bankName || !accountNumber || !accountName)}
          <p class="hint-warning">⚠️ Please complete all bank details</p>
        {:else if !isLoading && (!parseFloat(amount) || parseFloat(amount) < config.minAmount)}
          <p class="hint-muted">Enter an amount (min ${config.minAmount}) to continue</p>
        {/if}

        <!-- Footer -->
        <div class="form-footer">
          <div class="footer-note">
            <Clock size={11} />
            <span>{config.note}</span>
          </div>
          <a href="/wallet/history" class="history-link">
            <History size={11} />
            History
          </a>
        </div>

      </form>
    </div>
  </div>
</div>

<style>
  .page {
    min-height: 100vh;
    background: #f5f2eb;
    font-family: 'DM Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    padding-bottom: 4rem;
  }

  .container {
    max-width: 500px;
    margin: 0 auto;
    padding: 20px;
  }

  /* Balance Card */
  .balance-card {
    background: linear-gradient(135deg, #2c2418, #1a1610);
    border-radius: 24px;
    padding: 24px;
    margin-bottom: 20px;
  }

  .balance-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 16px;
  }

  .balance-label {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 11px;
    font-weight: 500;
    color: #b8ab9a;
    background: rgba(255, 255, 255, 0.1);
    padding: 5px 12px;
    border-radius: 40px;
  }

  .balance-chip {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 10px;
    font-weight: 500;
    color: #fef3c7;
    background: rgba(245, 158, 11, 0.2);
    padding: 4px 10px;
    border-radius: 40px;
  }

  .balance-amount {
    font-size: 44px;
    font-weight: 700;
    color: white;
    letter-spacing: -0.02em;
    margin-bottom: 6px;
  }

  .balance-sub {
    font-size: 12px;
    color: #b8ab9a;
  }

  /* Mode Toggle */
  .mode-toggle {
    display: flex;
    gap: 8px;
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 60px;
    padding: 4px;
    margin-bottom: 20px;
  }

  .mode-btn {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    padding: 12px;
    border: none;
    border-radius: 50px;
    font-size: 14px;
    font-weight: 600;
    background: transparent;
    color: #9b8f7e;
    cursor: pointer;
    transition: all 0.2s;
  }

  .mode-btn.active-deposit {
    background: #d1fae5;
    color: #065f46;
  }

  .mode-btn.active-withdraw {
    background: #fee2e2;
    color: #991b1b;
  }

  /* Action Card */
  .action-card {
    background: #fefcf8;
    border: 1px solid #e8e4dc;
    border-radius: 24px;
    padding: 24px;
  }

  /* Toasts */
  .toast {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px 14px;
    border-radius: 14px;
    font-size: 12px;
    margin-bottom: 20px;
  }

  .toast.success {
    background: #d1fae5;
    color: #065f46;
    border: 1px solid #a7f3d0;
  }

  .toast.error {
    background: #fee2e2;
    color: #991b1b;
    border: 1px solid #fecaca;
  }

  .toast button {
    margin-left: auto;
    background: none;
    border: none;
    cursor: pointer;
    color: inherit;
    opacity: 0.6;
  }

  /* Form Elements */
  .field {
    margin-bottom: 20px;
  }

  .field-label {
    display: block;
    font-size: 11px;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    color: #9b8f7e;
    margin-bottom: 8px;
  }

  .field-hint {
    font-size: 10px;
    color: #b8ab9a;
    margin-top: 6px;
  }

  /* Amount Input */
  .amount-wrapper {
    position: relative;
  }

  .currency-symbol {
    position: absolute;
    left: 14px;
    top: 50%;
    transform: translateY(-50%);
    font-size: 20px;
    font-weight: 500;
    color: #b8ab9a;
  }

  .amount-input {
    width: 100%;
    padding: 14px 14px 14px 36px;
    font-size: 24px;
    font-weight: 600;
    background: #fefdfb;
    border: 1.5px solid #e8e4dc;
    border-radius: 16px;
    outline: none;
    transition: all 0.2s;
  }

  .amount-input:focus {
    border-color: #d1cbbc;
  }

  .amount-input.deposit:focus {
    border-color: #10b981;
  }

  .amount-input.withdraw:focus {
    border-color: #ef4444;
  }

  .amount-input.error {
    border-color: #ef4444;
  }

  /* Quick Amounts */
  .quick-grid {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 8px;
  }

  .quick-btn {
    padding: 10px 0;
    background: #f0ebe3;
    border: none;
    border-radius: 12px;
    font-size: 12px;
    font-weight: 500;
    color: #4a3e2e;
    cursor: pointer;
    transition: all 0.2s;
  }

  .quick-btn:hover {
    background: #e8e0d6;
  }

  .quick-btn.active-deposit {
    background: #d1fae5;
    color: #065f46;
  }

  .quick-btn.active-withdraw {
    background: #fee2e2;
    color: #991b1b;
  }

  /* Payment Methods */
  .methods-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 8px;
  }

  .method-btn {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
    padding: 12px 8px;
    background: #f0ebe3;
    border: 1px solid #e8e4dc;
    border-radius: 14px;
    font-size: 11px;
    font-weight: 500;
    color: #9b8f7e;
    cursor: pointer;
    transition: all 0.2s;
  }

  .method-btn.active {
    background: #ede9fe;
    border-color: #c4b5fd;
    color: #6d28d9;
  }

  /* Bank Fields */
  .bank-fields {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .bank-input {
    padding: 12px 14px;
    background: #fefdfb;
    border: 1.5px solid #e8e4dc;
    border-radius: 14px;
    font-size: 14px;
    outline: none;
    transition: all 0.2s;
  }

  .bank-input:focus {
    border-color: #ef4444;
  }

  /* Submit Button */
  .submit-btn {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    padding: 14px;
    border: none;
    border-radius: 60px;
    font-size: 14px;
    font-weight: 600;
    color: white;
    cursor: pointer;
    transition: all 0.2s;
    margin-top: 8px;
  }

  .submit-btn.deposit {
    background: linear-gradient(135deg, #10b981, #059669);
  }

  .submit-btn.deposit:hover:not(:disabled) {
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3);
  }

  .submit-btn.withdraw {
    background: linear-gradient(135deg, #ef4444, #dc2626);
  }

  .submit-btn.withdraw:hover:not(:disabled) {
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(239, 68, 68, 0.3);
  }

  .submit-btn:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  .spinner {
    width: 16px;
    height: 16px;
    border: 2px solid rgba(255, 255, 255, 0.3);
    border-top-color: white;
    border-radius: 50%;
    animation: spin 0.7s linear infinite;
  }

  /* Hints */
  .hint-success {
    text-align: center;
    font-size: 11px;
    color: #10b981;
    margin-top: 12px;
  }

  .hint-warning {
    text-align: center;
    font-size: 11px;
    color: #f59e0b;
    margin-top: 12px;
  }

  .hint-muted {
    text-align: center;
    font-size: 11px;
    color: #b8ab9a;
    margin-top: 12px;
  }

  /* Form Footer */
  .form-footer {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 20px;
    padding-top: 16px;
    border-top: 1px solid #e8e4dc;
  }

  .footer-note {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 11px;
    color: #b8ab9a;
  }

  .history-link {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 11px;
    font-weight: 500;
    color: #9b8f7e;
    text-decoration: none;
    transition: color 0.2s;
  }

  .history-link:hover {
    color: #f59e0b;
  }

  /* Remove number input spinners */
  input[type="number"]::-webkit-inner-spin-button,
  input[type="number"]::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
  }

  input[type="number"] {
    -moz-appearance: textfield;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }
  @media (min-width: 600px){
    .page{
      padding-bottom: 6rem;
    }
  }

  @media (max-width: 480px) {
    .container {
      padding: 16px;
    }

    .balance-amount {
      font-size: 34px;
    }

    .quick-grid {
      gap: 6px;
    }

    .quick-btn {
      padding: 8px 0;
      font-size: 11px;
    }

    .methods-grid {
      gap: 6px;
    }

    .method-btn {
      padding: 10px 4px;
    }

    .action-card {
      padding: 18px;
    }
  }
</style>