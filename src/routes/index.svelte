<script>
	let formData;
	let MesError = 0;
	let MailError = 0;
	let Sending = 0;
	function checkValidate() {
		MesError = 0;
		MailError = 0;
		Sending = 0;

		let error = Validator();
		if (error === 0) {
			Sending = 1;
			const ToSend = {
				FMessage: formData.messege.value,
				FMail: formData.Email.value
			};

			fetch('/todos/server', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify(ToSend)
			})
				.then((resp) => {
					if (resp.ok) {
						console.log(resp);
						formData.reset();
					}
					Sending = 0;
					return resp.json();
				})
				.then((data) => {
					/*console.log('Cod : ' + data.code);
					console.log('Status : ' + data.message);*/
					alert(data.message);
				})
				.catch((e) => alert(e));
		}
	}

	function Validator() {
		let error = 0;
		let req = document.querySelectorAll('._req');
		for (let index = 0; index < req.length; index++) {
			const input = req[index];
			if (input.classList.contains('_gmail')) {
				if (ValidGmail(input)) {
					error++;
					MailError = 1;
				}
			} else if (input.value == '') {
				error++;
				MesError = 1;
			}
		}
		return error;
	}
	function ValidGmail(input) {
		return !/^\w+([-]?\w+)*@\w+([-]?\w+)*(\.\w{2,8})+$/.test(input.value);
	}
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<section>
	{#if Sending}
		<div class="sending" />
	{/if}
	<form bind:this={formData} on:submit|preventDefault={checkValidate} id="form">
		<h1 class="form_title">Відправка E-mail</h1>
		<div class="form_item">
			<label for="formMail" class="form_label">Електронна адреса:</label>
			<input id="formMail" type="text" name="Email" class="form_input _req _gmail" />
			{#if MailError}
				<div class="some_error"><b>🠕 Некоректна email!</b></div>
			{/if}
		</div>
		<div class="form_item">
			<label for="formMessege" class="form_label">Текст електронного листа:</label>

			<textarea name="messege" id="formMessege" class="form_input _req" />
			{#if MesError}
				<div class="some_error"><b>🠕 Повідомлення не може бути пустим!</b></div>
			{/if}
		</div>
		<button type="submit" class="form_item">Надіслати!</button>
	</form>
</section>

<style>
	:root {
		--error-color: #1708e6;
		--background-color: #c1c8e4;
		--text-color: #0a39af;
		--background-item-color: #d9dff8;
		--background-button-color: #e5eafd;
		--background-button-color2: #ccd6fa;
		--border-button-color: #1150f0;
	}

	.some_error {
		display: block;
		color: var(--error-color);
		font-size: 20px;
		margin: 10px;
	}

	.sending {
		position: absolute;
		content: '';
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background: rgba(70, 63, 180, 0.3) url('../loading.gif') center / 200px no-repeat;
	}

	form {
		max-width: 400px;
		margin: auto;
	}

	form * {
		outline: none;
	}

	.form_title {
		text-align: center;
		font-weight: 500;
	}

	.form_item {
		margin: 10px 0 15px 0;
	}

	.form_label {
		font-size: 20px;
		display: block;
		margin: 0 0 10px 0;
	}

	.form_input {
		border-radius: 10px;
		height: 50px;
		width: 100%;
		font-size: 20px;
		border-color: var(--text-color);
		border-width: 2px;
		padding: 10px;
		box-sizing: border-box;
	}

	.form_input:focus {
		box-shadow: 0 0 10px var(--border-button-color);
	}

	textarea {
		min-height: 100px;
		resize: vertical;
	}

	section {
		background-color: var(--background-color);
		color: var(--text-color);
		height: 100vh;
	}

	input,
	textarea,
	button {
		background-color: var(--background-item-color);
		color: var(--text-color);
	}

	input:-webkit-autofill {
		-webkit-text-fill-color: var(--text-color) !important;
	}

	button {
		width: 100%;
		min-height: 50px;
		background-color: var(--background-button-color);
		font-size: 25px;
		border-radius: 40px;
		border-color: var(--border-button-color);
		border-width: 3px;
		transition: background-color 0.5s;
		box-shadow: 0 3px 0 var(--text-color);
	}

	button:hover {
		background-color: var(--background-button-color2);
	}

	button:active {
		box-shadow: 0 0 0 var(--text-color);
	}
</style>
