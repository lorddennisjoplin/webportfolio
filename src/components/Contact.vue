<template>
	<section id="contact" class="mt-5">
		<h2 class="mb-4 text-center">Contact</h2>
		
		<div class="row">
			<div class="col-md-6 mb-md-4 mb-5 order-2 order-md-1">
				<div class="embed-responsive embed-responsive-16by9">
					<iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d15447.701317078461!2d121.02756064140182!3d14.54626262399787!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x3397c8fd1675787b%3A0x37729704b8460a64!2sForbes%20Park%2C%20Makati%20City%2C%20Metro%20Manila!5e0!3m2!1sen!2sph!4v1768188371593!5m2!1sen!2sph" width="600" height="450" style="border:0;width:100%; max-width:100%;" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe>
				</div>
			</div>
			<div class="col-md-6 mb-md-5 mb-4 order-1 order-md-2">
				<form @submit.prevent="submitForm">
					<div class="mb-3">
						<label for="name" class="form-label">Name</label>
						<input type="text" v-model="name" class="form-control" id="name" required>
					</div>

					<div class="mb-3">
						<label for="email" class="form-label">Email address</label>
						<input type="email" v-model="email" class="form-control" id="email" aria-describedby="emailHelp" required>
					</div>

					<div class="form-group">
						<label class="form-label" for="message">Message</label>
						<textarea v-model="message" class="form-control" id="message" rows="7" required></textarea>
					</div>

					<div class="mt-3">
						<div ref="recaptchaContainer"></div>
					</div>

					<button type="submit" class="btn btn-primary mt-3" :disabled="isLoading">
						{{ isLoading ? 'Sending...' : 'Submit' }}
					</button>

					

				</form>
			</div>
		</div>
	</section>
</template>

<script setup>
	import { ref, onMounted, onBeforeMount } from 'vue';
	import { Notyf } from 'notyf';
	import 'notyf/notyf.min.css';

	const notyf = new Notyf();

	const WEB3FORMS_ACCESS_KEY = 'db55d149-3ac1-4a2c-a297-0f490fa88810';

	const subject = "New message from Portfolio page";

	// Input initial values
	const name = ref('');
	const email = ref('');
	const message = ref('');

	const isLoading = ref(false);

	const submitForm = async () => {

		if (!recaptchaToken.value) {
			notyf.error("Verify that you're not a robot.")
			return;
		}

		isLoading.value = true;

		try {
			const response = await fetch('https://api.web3forms.com/submit', {
				method: 'POST',
				headers: {
					"Content-Type": "application/json",
					Accept: "application/json"
				},
				body: JSON.stringify({
					access_key: WEB3FORMS_ACCESS_KEY,
					subject: subject,
					name: name.value,
					email: email.value,
					message: message.value
				})
			});

			const result = await response.json();

			if (result.success) {
				console.log(result);

				isLoading.value = false;

				notyf.success('Message sent.');

				name.value = '';
				email.value = '';
				message.value = '';
			}
		}

		catch (error) {
			console.log(error);
			isLoading.value = false;

			notyf.error('Message not sent.');
		}

		finally {
			resetRecaptcha();
		}
	}

	const SITE_KEY = '6LfMsHEsAAAAAFjpIsVpc9xHHcbowVi-nQpVq9QO';
	// const SITE_SECRET_KEY = '6LfMsHEsAAAAADZ5w2gnYzgn_peL5ul5qJQRTjB8';

	const recaptchaContainer = ref(null);
	const recaptchaWidgetId = ref(null);
	const recaptchaToken = ref('');

	function onRecaptchaSuccess(token) {
		recaptchaToken.value = token;
	}

	function onRecaptchaExpired() {
		recaptchaToken.value = '';
	}

	function renderRecaptcha() {
	  if (!window.grecaptcha) {
	    console.error('reCAPTCHA not loaded');
	    return;
	  }

	  recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
	    sitekey: SITE_KEY,
	    size: 'normal', // or 'compact'
	    callback: onRecaptchaSuccess,
	    'expired-callback': onRecaptchaExpired,
	  });
	}

	function resetRecaptcha() {
	  if (recaptchaWidgetId.value !== null) {
	    window.grecaptcha.reset(recaptchaWidgetId.value);
	    recaptchaToken.value = '';
	  }
	}

	onMounted(() => {
		const interval = setInterval(() => {
			if (window.grecaptcha && window.grecaptcha.render) {
				renderRecaptcha();
				clearInterval(interval);
			}
		}, 100);

		onBeforeMount(() => {
			clearInterval(interval);
		})
	});

</script>