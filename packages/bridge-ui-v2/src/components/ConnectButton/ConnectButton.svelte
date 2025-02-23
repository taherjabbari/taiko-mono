<script lang="ts">
  import { onDestroy, onMount } from 'svelte';
  import { t } from 'svelte-i18n';

  import { Button } from '$components/Button';
  import { DesktopOrLarger } from '$components/DesktopOrLarger';
  import { EthIcon, Icon } from '$components/Icon';
  import { web3modal } from '$libs/connect';
  import { noop } from '$libs/util/noop';
  import { renderBalance } from '$libs/util/renderBalance';
  import { ethBalance } from '$stores/balance';

  export let connected = false;

  // Will help us to programmatically show or hide the balance of
  // the web3modal core button
  let isDesktopOrLarger: boolean;

  let web3modalOpen = false;
  let unsubscribeWeb3Modal = noop;

  function connectWallet() {
    if (web3modalOpen) return;
    web3modal.openModal();
  }

  function onWeb3Modal(state: { open: boolean }) {
    web3modalOpen = state.open;
  }

  onMount(() => {
    unsubscribeWeb3Modal = web3modal.subscribeModal(onWeb3Modal);
  });

  onDestroy(unsubscribeWeb3Modal);
</script>

<DesktopOrLarger bind:is={isDesktopOrLarger} />

<!-- 
  We are gonna make use of Web3Modal core button when we are connected,
  which comes with interesting features out of the box.
  https://docs.walletconnect.com/2.0/web/web3modal/html/wagmi/components
-->
{#if connected}
  <Button class="px-[20px] py-2 mr-[8px] rounded-full" type="neutral" on:click={connectWallet}>
    <span class="body-regular f-items-center space-x-2">
      <svelte:component this={EthIcon} size={20} />
      <span>{renderBalance($ethBalance)}</span>
    </span>
  </Button>
  <w3m-core-button balance="hide" />
{:else}
  <!-- TODO: fixing the width for English. i18n? -->
  <Button class="px-[20px] py-2 rounded-full w-[215px]" type="neutral" loading={web3modalOpen} on:click={connectWallet}>
    <span class="body-regular f-items-center space-x-2">
      {#if web3modalOpen}
        <span>{$t('wallet.status.connecting')}</span>
      {:else}
        <Icon type="user-circle" class="md-show-block" />
        <span>{$t('wallet.connect')}</span>
      {/if}
    </span>
  </Button>
{/if}
