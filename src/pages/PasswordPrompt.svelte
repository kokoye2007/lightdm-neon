<script lang="ts">
    import { onMount } from "svelte";
    import { push, replace } from "svelte-spa-router";
    import Container from "../components/Container.svelte";
    import Input from "../components/Input.svelte";
    import UserImage from "../components/UserImage.svelte";
    import {
        authenticating,
        selectedSession,
        sessionTouched,
    } from "../store/runtime";

    let user = window.lightdm.users.find(
        (u) => u.username === window.lightdm.authentication_user,
    );
    let password: string = "";

    onMount(() => {
        if (!user) {
            replace("/select-user");
            return;
        }
        if (
            user.session &&
            $selectedSession !== user.session &&
            !$sessionTouched
        ) {
            $selectedSession = user.session;
        }
    });

    function submit() {
        if (!$authenticating) {
            $authenticating = true;
            window.lightdm.respond(password);
        }
    }

    function cancel() {
        $authenticating = false;
        window.lightdm.cancel_authentication();
        push("/select-user");
    }
</script>

<Container backFn={cancel}>
    <div class="center-wrapper">
        {#if user}
            <div id="pw-prompt">
                <div class="login-title">
                    <span>Login to</span>
                    <span class="user-image-inline"
                        ><UserImage bind:user /></span
                    >
                    <span class="user-name">{user.display_name}</span>
                </div>
                <div class="input-row">
                    <Input
                        type="password"
                        placeholder="Password"
                        bind:value={password}
                        on:enter={submit}
                        icon=""
                        withSubmit={false}
                        autofocus
                        loading={$authenticating}
                    />
                    <button
                        class="submit-btn"
                        on:click={submit}
                        disabled={$authenticating}
                    >
                        <svg
                            width="50"
                            height="50"
                            viewBox="0 0 32 32"
                            fill="none"
                        >
                            <circle
                                cx="16"
                                cy="16"
                                r="15"
                                stroke="white"
                                stroke-width="2"
                            />
                            <path
                                d="M12 16H20M20 16L17 13M20 16L17 19"
                                stroke="white"
                                stroke-width="2"
                                stroke-linecap="round"
                                stroke-linejoin="round"
                            />
                        </svg>
                    </button>
                </div>
                <div class="login-links">
                    <span>
                        Forgotten your GalacticID? <a
                            href="#"
                            class="reset-link">Reset your password</a
                        >
                    </span>
                </div>
            </div>
        {/if}
    </div>
</Container>

<!-- Floating Switch Account Button -->
<button class="switch-account-fab" on:click={cancel}>
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
        <circle cx="12" cy="8" r="4" stroke="white" stroke-width="2" />
        <path
            d="M4 20c0-2.21 3.58-4 8-4s8 1.79 8 4"
            stroke="white"
            stroke-width="2"
            stroke-linecap="round"
        />
    </svg>
    <span>Switch Account</span>
</button>

<style>
    @font-face {
        font-family: "Exo 2";
        src: url("/assets/fonts/Exo2-Regular.otf") format("opentype");
        font-weight: 400;
        font-style: normal;
    }
    @font-face {
        font-family: "Exo 2";
        src: url("/assets/fonts/Exo2-Bold.otf") format("opentype");
        font-weight: 700;
        font-style: normal;
    }
    @font-face {
        font-family: "Exo 2";
        src: url("/assets/fonts/Exo2-Italic.otf") format("opentype");
        font-weight: 400;
        font-style: italic;
    }
    @font-face {
        font-family: "Exo 2";
        src: url("/assets/fonts/Exo2-BoldItalic.otf") format("opentype");
        font-weight: 700;
        font-style: italic;
    }
    :global(body),
    .center-wrapper,
    #pw-prompt {
        font-family: "Exo 2", "Segoe UI", Arial, sans-serif;
    }
    .center-wrapper {
        display: flex;
        justify-content: center;
        align-items: center;
        min-height: 100vh;
        width: 100vw;
    }
    #pw-prompt {
        @apply flex flex-col items-start gap-8 w-full max-w-xl;
        margin-top: 0;
        margin-left: -80vh;
        background: none;
        max-width: 32rem;
    }
    .login-title {
        display: flex;
        align-items: center;
        font-size: 2.2rem;
        color: #fff;
        font-weight: 400;
        gap: 1rem;
        margin-bottom: 2rem;
    }
    .user-image-inline {
        display: inline-flex;
        align-items: center;
        margin: 0 0.5rem;
    }
    .user-image-inline :global(.picture) {
        width: 48px;
        height: 48px;
        border-radius: 50%;
        overflow: hidden;
        border: 2px solid #fff;
    }
    .user-name {
        font-weight: 500;
        font-size: 2.2rem;
        color: #fff;
    }
    .input-row {
        display: flex;
        align-items: center;
        width: 100%;
        gap: 1rem;
    }
    .input-row :global(input) {
        font-size: 1.2rem;
        padding: 1.2rem 1.5rem;
        border-radius: 1rem;
        background: rgba(30, 30, 30, 0.7);
        border: 1px solid #333;
        color: #fff;
        width: 28rem;
        outline: none;
    }
    .submit-btn {
        background: none;
        border-radius: 50%;
        width: 56px;
        height: 56px;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
        transition: background 0.2s;
    }
    .submit-btn:disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }
    .submit-btn:hover {
        background: rgba(255, 255, 255, 0.08);
    }
    .login-links {
        margin-top: 1.5rem;
        color: #fff;
        font-size: 1rem;
        opacity: 0.8;
    }
    .reset-link {
        color: #ffb300;
        text-decoration: none;
        margin-left: 0.2rem;
    }
    .reset-link:hover {
        text-decoration: underline;
    }
    .switch-account-fab {
        position: fixed;
        right: 2rem;
        bottom: 2rem;
        display: flex;
        align-items: center;
        gap: 0.5rem;
        background: rgba(30, 30, 30, 0.85);
        color: #fff;
        border: none;
        border-radius: 2rem;
        padding: 0.75rem 1.5rem;
        box-shadow: 0 4px 16px rgba(0, 0, 0, 0.25);
        font-size: 1rem;
        cursor: pointer;
        z-index: 1000;
        transition:
            background 0.2s,
            box-shadow 0.2s;
    }
    .switch-account-fab:hover {
        background: rgba(255, 255, 255, 0.12);
        box-shadow: 0 6px 24px rgba(0, 0, 0, 0.35);
    }
    .switch-account-fab svg {
        display: block;
    }
</style>
