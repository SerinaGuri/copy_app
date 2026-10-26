<script>
    let { children, author = "Not Found" } = $props();
    let quoteElement;

    async function copyQuote() {
        try {
            await navigator.clipboard.writeText(
                quoteElement.innerText.trim()
            );

            if ("Notification" in window) {
                let permission = Notification.permission;

                if (permission === "default") {
                    permission = await Notification.requestPermission();
                }

                if (permission === "granted") {
                    new Notification("Quote copied!");
                }
            }
        } catch (error) {
            console.error(error);
        }
    }

    async function share() {
        try {
            await navigator.share({
                title: "Quote",
                text: quoteElement.innerText.trim()
            });
        } catch (error) {
            if (error.name !== "AbortError") {
                console.error(error);
            }
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
</div>
