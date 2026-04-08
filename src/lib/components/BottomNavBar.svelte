<script lang="ts">
  import { Home, Wallet, Trophy, Sparkles } from 'lucide-svelte';
  import { page } from '$app/stores';
  import type { Component } from 'svelte';

  type NavItem = {
    name: string;
    href: string;
    icon: Component;
    activePaths?: string[];
  };

  const navItems: NavItem[] = [
    { name: 'Home', href: '/', icon: Home, activePaths: ['/'] },
    { name: 'Winners', href: '/winners', icon: Trophy, activePaths: ['/winners'] },
    { name: 'Wallet', href: '/wallet/deposit', icon: Wallet, activePaths: ['/wallet/deposit', '/wallet/withdraw'] }
  ];

  function isActive(item: NavItem): boolean {
    const pathname = $page.url.pathname;
    return item.activePaths?.includes(pathname) || pathname === item.href;
  }
</script>

{#if ['/', '/winners', '/wallet/deposit', '/wallet/withdraw'].includes($page.url.pathname)}
  <div class="bottom-nav">
    {#each navItems as item}
      {@const Icon = item.icon}
      {@const active = isActive(item)}
      <a
        href={item.href}
        class="nav-item {active ? 'active' : ''}"
        class:active
      >
        <div class="nav-icon-wrapper">
          <Icon size={20} />
        </div>
        <span class="nav-label">{item.name}</span>
        {#if active}
          <div class="active-dot"></div>
        {/if}
      </a>
    {/each}
  </div>
{/if}

<style>
  .bottom-nav {
    position: fixed;
    bottom: 16px;
    left: 50%;
    transform: translateX(-50%);
    width: calc(100% - 32px);
    max-width: 320px;
    /*background: #fefcf8;*/
    border: 1px solid #e8e4dc;
    border-radius: 60px;
    padding: 8px 16px;
    display: flex;
    align-items: center;
    justify-content: space-around;
    gap: 8px;
    backdrop-filter: blur(20px);
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
    z-index: 50;
  }

  .nav-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    padding: 8px 20px;
    border-radius: 40px;
    text-decoration: none;
    transition: all 0.2s ease;
    position: relative;
    flex: 1;
  }

  .nav-item:hover {
    background: #f0ebe3;
  }

  .nav-item.active {
    background: #fef3c7;
  }

  .nav-icon-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    color: #9b8f7e;
    transition: color 0.2s;
  }

  .nav-item.active .nav-icon-wrapper {
    color: #f59e0b;
  }

  .nav-label {
    font-size: 10px;
    font-weight: 500;
    color: #9b8f7e;
    transition: color 0.2s;
  }

  .nav-item.active .nav-label {
    color: #f59e0b;
    font-weight: 600;
  }

  .active-dot {
    position: absolute;
    bottom: 2px;
    left: 50%;
    transform: translateX(-50%);
    width: 4px;
    height: 4px;
    background: #f59e0b;
    border-radius: 50%;
  }

  @media (max-width: 480px) {
    .bottom-nav {
      bottom: 12px;
      padding: 6px 12px;
      width: calc(100% - 24px);
      max-width: 280px;
    }

    .nav-item {
      padding: 6px 12px;
    }

    .nav-label {
      font-size: 9px;
    }

    .nav-icon-wrapper svg {
      width: 18px;
      height: 18px;
    }
  }

  @media (min-width: 768px) {
    .bottom-nav {
      max-width: 360px;
      bottom: 20px;
    }

    .nav-item {
      padding: 10px 24px;
    }

    .nav-label {
      font-size: 11px;
    }
  }
</style>