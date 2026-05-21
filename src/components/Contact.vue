<script setup>
    import { ref, onMounted, onBeforeUnmount } from 'vue';
    import { Notyf } from 'notyf';
    import 'notyf/notyf.min.css';

    const notyf = new Notyf();

    // Credentials
    const WEB3FORMS_ACCESS_KEY = "76f2c8b7-9af0-4626-9dbc-6fa605e309d7";
    const SITE_KEY = '6LcOI_UsAAAAAL1Z5e2Udt1OIIS1pvJmSCJcK-pm';

    // Form State
    const email = ref("");
    const subject = ref("Project Inquiry"); // Default value matching your placeholder
    const message = ref("");
    const isLoading = ref(false);

    // reCAPTCHA State
    const recaptchaContainer = ref(null);
    const recaptchaWidgetId = ref(null);
    const recaptchaToken = ref('');

    const submitForm = async () => {
        if (!recaptchaToken.value) {
            notyf.error('Please verify that you are not a robot.');
            return;
        }

        isLoading.value = true;

        try {
            const response = await fetch("https://api.web3forms.com/submit", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    Accept: "application/json"
                },
                body: JSON.stringify({
                    access_key: WEB3FORMS_ACCESS_KEY,
                    subject: subject.value,
                    email: email.value,
                    message: message.value,
                    "g-recaptcha-response": recaptchaToken.value // Passes token verification to Web3Forms
                })
            });

            const result = await response.json();

            if (result.success) {
                notyf.success("Message sent successfully!");
                clearForm();
            } else {
                notyf.error(result.message || "Failed to send message.");
            }

        } catch (error) {
            console.error(error);
            notyf.error("Failed to send message");
        } finally {
            isLoading.value = false;
            resetRecaptcha();
        }
    }

    const clearForm = () => {
        email.value = "";
        subject.value = "";
        message.value = "";
    };

    /* reCAPTCHA Functions */
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
            size: 'normal',
            callback: onRecaptchaSuccess,
            'expired-callback': onRecaptchaExpired
        });
    }

    function resetRecaptcha() {
        if (recaptchaWidgetId.value !== null) {
            window.grecaptcha.reset(recaptchaWidgetId.value);
            recaptchaToken.value = '';
        }
    }

    // Safe interval polling for global reCAPTCHA initialization
    let scriptCheckInterval = null;

    onMounted(() => {
        scriptCheckInterval = setInterval(() => {
            if (window.grecaptcha && window.grecaptcha.render) {
                renderRecaptcha();
                clearInterval(scriptCheckInterval);
            }
        }, 100);
    });

    onBeforeUnmount(() => {
        if (scriptCheckInterval) clearInterval(scriptCheckInterval);
    });
</script>

<template>
    <section id="contact" class="row g-4 align-items-start">
        <div class="col-12 col-lg-6">
            <div class="bg-surface-dim p-1 beveled-out">
                <div class="bg-primary-custom text-white px-2 py-1 d-flex justify-content-between align-items-center mb-1">
                    <span class="fw-bold d-flex align-items-center gap-2 small-font-window">
                        <span class="material-symbols-outlined fs-6">mail</span> SEND_MESSAGE.EXE
                    </span>
                    <div class="d-flex gap-1">
                        <div class="window-control" @click="clearForm">X</div>
                    </div>
                </div>
                
                <form @submit.prevent="submitForm" class="p-4 bg-surface-container-low d-flex flex-column gap-3 beveled-in">
                    <div class="d-flex flex-column gap-1">
                        <label class="text-xs fw-bold text-uppercase" for="emailInput">From:</label>
                        <input id="emailInput" v-model="email" class="form-control-retro beveled-in px-2 py-1" placeholder="your_email@provider.com" type="email" required/>
                    </div>
                    
                    <div class="d-flex flex-column gap-1">
                        <label class="text-xs fw-bold text-uppercase" for="subjectInput">Subject:</label>
                        <input id="subjectInput" v-model="subject" class="form-control-retro beveled-in px-2 py-1" placeholder="Project Inquiry" type="text" required/>
                    </div>
                    
                    <div class="d-flex flex-column gap-1">
                        <label class="text-xs fw-bold text-uppercase" for="messageInput">Message Body:</label>
                        <textarea id="messageInput" v-model="message" class="form-control-retro beveled-in px-2 py-1" placeholder="Hello Gerald..." rows="4" required></textarea>
                    </div>

                    <div class="d-flex justify-content-start pt-2">
                        <div ref="recaptchaContainer"></div>
                    </div>
                    
                    <div class="d-flex justify-content-end gap-2 pt-2">
                        <button type="submit" class="retro-btn px-4 py-1 text-xs fw-bold" :disabled="isLoading">
                            {{ isLoading ? "SENDING..." : "SEND" }}
                        </button>
                        <button type="button" @click="clearForm" class="retro-btn px-4 py-1 text-xs fw-bold">CANCEL</button>
                    </div>
                </form>
            </div>
        </div>
        
        <div class="col-12 col-lg-6 d-flex flex-column gap-4">
            <div class="bg-surface-dim p-1 beveled-out position-relative h-256px">
                <div class="position-absolute top-0 start-0 m-1 bg-primary-custom text-white px-2 py-1 text-xs fw-bold z-1">LOCATION_MAP.MAP</div>
                <iframe class="w-100 h-100 object-fit-cover grayscale border-0" alt="Map View" src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d15456.801158983038!2d120.97022741637782!3d14.41561459039371!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x3397d188325d8cf3%3A0x852b7710509fb321!2sMolino%20VI%2C%20Bacoor%2C%20Cavite!5e0!3m2!1sen!2sph!4v1775636986722!5m2!1sen!2sph" width="600" height="450" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe>
            </div>
            
            <div class="bg-white p-3 beveled-in d-flex flex-column gap-3">
                <h3 class="fw-bold text-primary-custom border-bottom border-2 border-primary-container pb-1 text-uppercase fs-6 m-0">Contact Directory</h3>
                <ul class="list-unstyled d-flex flex-column gap-2 m-0 font-body fs-6">
                    <li class="d-flex align-items-center gap-2">
                        <span class="material-symbols-outlined text-secondary-custom fs-5">alternate_email</span>
                        <span class="fw-bold">EMAIL:</span> gerald.valencia@deped.gov.ph
                    </li>
                    <li class="d-flex align-items-center gap-2">
                        <span class="material-symbols-outlined text-secondary-custom fs-5">hub</span>
                        <span class="fw-bold">GITHUB:</span> /github.com/LPEastHub
                    </li>
                    <li class="d-flex align-items-center gap-2">
                        <span class="material-symbols-outlined text-secondary-custom fs-5">link</span>
                        <span class="fw-bold">LINKEDIN:</span> /in/gerald-valencia-513105216/
                    </li>
                </ul>
            </div>
        </div>
    </section>
</template>