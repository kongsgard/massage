<script lang="ts">
	import { Label } from '$lib/components/ui/label';
	import { Switch } from '$lib/components/ui/switch';
	import { onDestroy } from 'svelte';

	let isScreenOn = $state(false);
	let wakeLock = $state<WakeLockSentinel | null>(null);
	let releaseListener = $state<(() => void) | null>(null);

	async function toggleScreenOn() {
		try {
			if (!isScreenOn) {
				wakeLock = await navigator.wakeLock?.request('screen');

				releaseListener = () => {
					console.log('Wake lock released');
					isScreenOn = false;
				};

				wakeLock?.addEventListener('release', releaseListener);
				isScreenOn = true;
			} else {
				if (releaseListener && wakeLock) {
					wakeLock.removeEventListener('release', releaseListener);
					releaseListener = null;
				}

				await wakeLock?.release();
				wakeLock = null;
				isScreenOn = false;
			}
		} catch (error) {
			console.error('Failed to toggle screen on:', error);
		}
	}

	onDestroy(() => {
		if (!isScreenOn && releaseListener && wakeLock) {
			wakeLock.removeEventListener('release', releaseListener);
			releaseListener = null;
		}
	});
</script>

<div class="flex items-center space-x-2">
	<Switch id="screen-toggle" checked={isScreenOn} onchange={() => toggleScreenOn()} />
	<Label for="screen-toggle">Keep screen on</Label>
</div>
