<script lang="ts">
	import { goto } from '$app/navigation';
	import { session } from '$app/stores';
	import Loading from '$lib/components/Loading.svelte';

	const data = ['category 1', 'category 2', 'category 3'];
	let password,
		eye,
		eyeSlash,
		userFound = true,
		incorrectPassword = true,
		loading = false;

	const toggleVisibility = () => {
		if (password.type === 'password') {
			password.type = 'text';
			eye.classList.add('hidden');
			eyeSlash.classList.remove('hidden');
		} else {
			password.type = 'password';
			eye.classList.remove('hidden');
			eyeSlash.classList.add('hidden');
		}
	};

	let checked = true;
	let role = 'student';
	let placeholder;
	$: placeholder = role.charAt(0).toUpperCase() + role.slice(1);
	const selectRole = (event) => {
		const btn = event.target;
		const input = btn.childNodes[0];
		role = input.value;
		if (!input.checked) {
			checked = !checked;
		}
	};

	const handleSubmit = async (e) => {
		loading = true;
		const formData = new FormData(e.target);
		const data = {};
		for (let field of formData) {
			const [key, value] = field;
			data[key] = value;
		}
		const res = await fetch('/api/login', {
			method: 'POST',
			body: JSON.stringify(data)
		});
		const body = await res.json();
		if(body.ok) {
			$session.user = body.user;
			await goto(`/${body.user.role}`);
		} else {
			if(body.message === 'User not found') {
				userFound = false
			} else if(body.message === 'Incorrect password') {
				incorrectPassword = false
			}
		}
		loading = false;
	};
</script>

{#if loading}
	<Loading />
{/if}

<nav class="py-2 flex items-center justify-around">
	<h2 class="font-bold hover:underline"><a href="/">Pragati</a></h2>
	<ul class="flex justify-evenly">
		{#each data as piece}
			<li class="mx-4 hover:underline">
				<a href="/">{piece}</a>
			</li>
		{/each}
	</ul>
	<div>
		<button
			class="py-3 px-10 m-px bg-black text-white rounded hover:text-black hover:bg-white duration-300 border-black border"
			>Sign Up</button
		>
	</div>
</nav>

<div class="w-screen flex justify-center">
	<main class="w-1/2">
		<div class="my-8">
			<h2 class="text-center font-bold text-4xl tracking-wide">Login to Your Account</h2>
			<p class="text-center">
				Lorem ipsum dolor, sit amet consectetur adipisicing elit. Quis molestias praesentium
				sapiente obcaecati rem cum? Veritatis iure corporis pariatur animi?
			</p>
		</div>

		<form
			on:submit|preventDefault={handleSubmit}
			class="flex justify-center items-center flex-col gap-y-10 p-10"
		>
			<div class="flex h-12 w-1/2">
				<button
					on:click|preventDefault={selectRole}
					class:border={role === 'student'}
					class:border-b-black={role === 'student'}
					class="cursor-pointer h-full w-full flex justify-center items-center hover:bg-gray-200 focus:outline-none focus:border focus:border-b-black hover:border-b-black hover:border"
				>
					<input
						type="radio"
						id="student"
						name="role"
						value="student"
						class="pointer-events-none hidden"
						required
						{checked}
					/>
					<label for="student" class="cursor-pointer pointer-events-none">Student</label>
				</button>

				<button
					on:click|preventDefault={selectRole}
					class:border={role === 'teacher'}
					class:border-b-black={role === 'teacher'}
					class="cursor-pointer h-full w-full flex justify-center items-center hover:bg-gray-200 focus:outline-none focus:border focus:border-b-black hover:border-b-black hover:border"
				>
					<input
						type="radio"
						id="teacher"
						name="role"
						value="teacher"
						class="pointer-events-none hidden"
						required
						checked={!checked}
					/>
					<label for="teacher" class="cursor-pointer pointer-events-none">Teacher</label>
				</button>
			</div>

			<input
				type="text"
				name="id"
				placeholder={`${placeholder} ID`}
				required
				class="py-2 px-4 w-1/2 rounded outline-none border border-black"
				pattern="\d\d[a-zA-Z]\d\d[a-zA-Z]\d\d\d\d"
			/>
			{#if !userFound}
				<p>User not found!!!</p>
			{/if}
			<div class="w-1/2 flex">
				<input
					type="password"
					name="password"
					placeholder="Password"
					required
					bind:this={password}
					class="py-2 pl-4 pr-10 w-full rounded outline-none border border-black"
				/>
				<svg
					class="w-4 -ml-8 cursor-pointer"
					on:click={toggleVisibility}
					bind:this={eye}
					viewBox="0 0 576 512"
				>
					<path
						d="M160 256C160 185.3 217.3 128 288 128C358.7 128 416 185.3 416 256C416 326.7 358.7 384 288 384C217.3 384 160 326.7 160 256zM288 336C332.2 336 368 300.2 368 256C368 211.8 332.2 176 288 176C287.3 176 286.7 176 285.1 176C287.3 181.1 288 186.5 288 192C288 227.3 259.3 256 224 256C218.5 256 213.1 255.3 208 253.1C208 254.7 208 255.3 208 255.1C208 300.2 243.8 336 288 336L288 336zM95.42 112.6C142.5 68.84 207.2 32 288 32C368.8 32 433.5 68.84 480.6 112.6C527.4 156 558.7 207.1 573.5 243.7C576.8 251.6 576.8 260.4 573.5 268.3C558.7 304 527.4 355.1 480.6 399.4C433.5 443.2 368.8 480 288 480C207.2 480 142.5 443.2 95.42 399.4C48.62 355.1 17.34 304 2.461 268.3C-.8205 260.4-.8205 251.6 2.461 243.7C17.34 207.1 48.62 156 95.42 112.6V112.6zM288 80C222.8 80 169.2 109.6 128.1 147.7C89.6 183.5 63.02 225.1 49.44 256C63.02 286 89.6 328.5 128.1 364.3C169.2 402.4 222.8 432 288 432C353.2 432 406.8 402.4 447.9 364.3C486.4 328.5 512.1 286 526.6 256C512.1 225.1 486.4 183.5 447.9 147.7C406.8 109.6 353.2 80 288 80V80z"
					/>
				</svg>

				<svg
					class="w-4 -ml-8 cursor-pointer hidden"
					on:click={toggleVisibility}
					bind:this={eyeSlash}
					viewBox="0 0 640 512"
				>
					<path
						d="M150.7 92.77C195 58.27 251.8 32 320 32C400.8 32 465.5 68.84 512.6 112.6C559.4 156 590.7 207.1 605.5 243.7C608.8 251.6 608.8 260.4 605.5 268.3C592.1 300.6 565.2 346.1 525.6 386.7L630.8 469.1C641.2 477.3 643.1 492.4 634.9 502.8C626.7 513.2 611.6 515.1 601.2 506.9L9.196 42.89C-1.236 34.71-3.065 19.63 5.112 9.196C13.29-1.236 28.37-3.065 38.81 5.112L150.7 92.77zM189.8 123.5L235.8 159.5C258.3 139.9 287.8 128 320 128C390.7 128 448 185.3 448 256C448 277.2 442.9 297.1 433.8 314.7L487.6 356.9C521.1 322.8 545.9 283.1 558.6 256C544.1 225.1 518.4 183.5 479.9 147.7C438.8 109.6 385.2 79.1 320 79.1C269.5 79.1 225.1 97.73 189.8 123.5L189.8 123.5zM394.9 284.2C398.2 275.4 400 265.9 400 255.1C400 211.8 364.2 175.1 320 175.1C319.3 175.1 318.7 176 317.1 176C319.3 181.1 320 186.5 320 191.1C320 202.2 317.6 211.8 313.4 220.3L394.9 284.2zM404.3 414.5L446.2 447.5C409.9 467.1 367.8 480 320 480C239.2 480 174.5 443.2 127.4 399.4C80.62 355.1 49.34 304 34.46 268.3C31.18 260.4 31.18 251.6 34.46 243.7C44 220.8 60.29 191.2 83.09 161.5L120.8 191.2C102.1 214.5 89.76 237.6 81.45 255.1C95.02 286 121.6 328.5 160.1 364.3C201.2 402.4 254.8 432 320 432C350.7 432 378.8 425.4 404.3 414.5H404.3zM192 255.1C192 253.1 192.1 250.3 192.3 247.5L248.4 291.7C258.9 312.8 278.5 328.6 302 333.1L358.2 378.2C346.1 381.1 333.3 384 319.1 384C249.3 384 191.1 326.7 191.1 255.1H192z"
					/>
				</svg>
			</div>
			{#if !incorrectPassword}
				<p>Incorrect Password</p>
			{/if}
			<input
				type="submit"
				value="Login to your account"
				class="py-2 px-8 min-w-1/2 rounded outline-none cursor-pointer bg-black text-white border border-black hover:bg-white hover:text-black hover:px-12 duration-300"
			/>
		</form>
	</main>
</div>
