<script lang="ts">
  import { Home, Wallet, Trophy } from 'lucide-svelte';
  import { page } from '$app/stores';
  import type { Component } from 'svelte';

  type NavItem = {
    name: string;
    href: string;
    icon: Component;
  };

  const navItems: NavItem[] = [
    { name: 'Home', href: '/', icon: Home },
    { name: 'Winners', href: '/winners', icon: Trophy },
    { name: 'Wallet', href: '/wallet/deposit', icon: Wallet }
  ];

  function isActive(item: NavItem): boolean {
    const pathname = $page.url.pathname;
    if (item.name === 'Wallet') {
      return pathname === '/wallet/deposit' || pathname === '/wallet/withdraw';
    }
    return pathname === item.href;
  }
</script>

{#if ['/', '/winners', '/wallet/deposit', '/wallet/withdraw'].includes($page.url.pathname)}
  <div class="fixed w-4/5 md:w-1/3 border-2 border-gray-500 rounded-full bottom-3 left-1/2 -translate-x-1/2 flex items-center justify-around py-2 px-3 shadow-xl shadow-black/20 bg-white/80 backdrop-blur-sm z-50">
    {#each navItems as item}
      {@const Icon = item.icon}
      {@const active = isActive(item)}
      <div class={`rounded-full py-2 px-4 ${active ? 'bg-gray-300' : ''}`}>
        <a class={`flex flex-col place-items-center ${active ? 'text-blue-500' : 'text-gray-600'}`} href={item.href}>
          <Icon />
          <span class="text-[12px]">{item.name}</span>
        </a>
      </div>
    {/each}
  </div>
{/if}