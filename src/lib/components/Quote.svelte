<script>
    let { children, author = "Not known" } = $props();
    let quoteElement;
    let toast = $state("");
 
    function showToast(message) {
        toast = message;
        setTimeout(() => (toast = ""), 2500);
    }
 
    async function notify(message) {
        // Fall back to an in-page toast if notifications aren't possible
        if (!("Notification" in window)) {
            showToast(message);
            return;
        }
 
        let permission = Notification.permission;
        if (permission === "default") {
            permission = await Notification.requestPermission();
        }
 
        if (permission !== "granted") {
            showToast(message);
            return;
        }
 
        try {
            // Works on mobile Chrome when a service worker is registered
            const reg = await navigator.serviceWorker?.getRegistration();
            if (reg) {
                await reg.showNotification(message);
            } else {
                new Notification(message);
            }
        } catch {
            showToast(message);
        }
    }
 
    async function copyQuote() {
        const text = quoteElement.innerText.trim();
 
        try {
            if (navigator.clipboard && window.isSecureContext) {
                await navigator.clipboard.writeText(text);
            } else {
                // Fallback for non-HTTPS contexts
                const ta = document.createElement("textarea");
                ta.value = text;
                ta.style.position = "fixed";
                ta.style.opacity = "0";
                document.body.appendChild(ta);
                ta.select();
                document.execCommand("copy");
                document.body.removeChild(ta);
            }
        } catch {
            showToast("Couldn't copy the quote");
            return;
        }
 
        await notify("You successfully copied the quote to your clipboard!");
    }
 
    async function share() {
        if (!navigator.share) {
            showToast("Sharing isn't supported on this device");
            return;
        }
        try {
            await navigator.share({
                title: "Quote",
                text: quoteElement.innerText.trim(),
            });
        } catch {
            // user cancelled the share sheet; ignore
        }
    }
</script>

<div class="border border-gray-200 border-l-4 border-l-pink-400 bg-white p-5">
    <header class="mb-3 font-medium text-gray-800">
        {author} says:
    </header>

    <blockquote
        bind:this={quoteElement}
        class="bg-gray-50 p-4 text-gray-700"
    >
        {@render children?.()}
    </blockquote>

    <div class="mt-4 flex gap-3">
        <button
            class="bg-pink-500 px-3 py-1.5 text-sm text-white hover:bg-pink-600"
            type="button"
            onclick={copyQuote}
        >
            Copy quote
        </button>

        <button
            class="border border-gray-300 px-3 py-1.5 text-sm text-gray-700 hover:bg-gray-100"
            type="button"
            onclick={share}
        >
            Share quote
        </button>
    </div>

    {#if toast}
        <p
            class="mt-3 border-l-4 border-pink-400 bg-gray-50 px-3 py-2 text-sm text-gray-700"
            role="status"
            aria-live="polite"
        >
            {toast}
        </p>
    {/if}
</div>
