<script lang="ts">
	import { goto } from '$app/navigation';

	import { writable } from 'svelte/store';
	import Button from '../components/Button.svelte';
	import TextInput from '../components/Input.svelte';
	import { email, password, status } from '../stores/auth';
	import { toast } from '../stores/toast';
	import { signIn, userStore } from '../supabase.client';
	const showPassword = writable(false);
	async function handleSignIn(email: string, password: string) {
		try {
			status.set('loading');
			const { error } = await signIn(email, password);
			if (error && error.status !== 406) {
				toast.danger('Login ou senha inválidos');
				status.set('error');
				return;
			}
			toast.success('Bem vindo: ' + $userStore.name);
			console.log('usuario', $userStore);
			status.set('success');
		} catch (error) {
			toast.danger('Login ou senha inválidos');
			status.set('error');
		}
	}
	$: if ($userStore) {
		setTimeout(() => {
			goto('/bills/month');
		}, 2000);
	}
</script>

<main>
	<div class="inputList">
		<TextInput bind:value={$email} placeholder="Digite um email válido" />
		<TextInput password={!$showPassword} bind:value={$password} placeholder="Digite uma senha" />
		<div class="showPassword">
			<input type="checkbox" bind:checked={$showPassword} placeholder="Mostrar senha" />
			<span>Mostrar senha</span>
		</div>
	</div>
	<Button status={$status} on:click={() => handleSignIn($email, $password)}>Entrar</Button>
</main>

<style lang="scss">
	main {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 70vw;
		min-width: 200px;
		max-width: 400px;
		flex-direction: column;
		gap: 1.5rem;
		:global button {
			width: 100%;
		}
		min-height: 80vh;
	}
	.inputList {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
		align-items: center;
		justify-content: center;
		width: 100%;
	}
	.showPassword {
		display: flex;
		align-items: center;
		justify-content: flex-start;
		width: 100%;
		gap: 0.5rem;
		font-size: small;
	}
</style>
