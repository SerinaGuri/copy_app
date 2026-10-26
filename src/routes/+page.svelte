<script>
    import Quote from '#lib/components/Quote.svelte';
</script>

<svelte:head>
    <title>Data Exchange</title>
</svelte:head>

<main class="min-h-screen bg-gray-50 px-4 py-10 sm:px-8">
    <h1 class="mb-3 text-center text-3xl font-bold text-gray-800 sm:text-4xl">
        Data Exchange
    </h1>

    <p class="mb-8 text-center text-gray-500">
        A collection of quotes and inspiration.
    </p>

    <div class="mx-auto grid max-w-5xl grid-cols-1 gap-5 sm:grid-cols-2 lg:grid-cols-3">
        <Quote author="Serina Guri">
            Dont be captain obvious
        </Quote>

        <Quote>
            Another inspiration
        </Quote>

        <Quote author="Oscar Wilde">
            Be yourself; everyone else is already taken.
        </Quote>

        <Quote author="Maya Angelou">
            Nothing will work unless you do.
        </Quote>

        <Quote author="Lao Tzu">
            A journey of a thousand miles begins with a single step.
        </Quote>

        <Quote author="Albert Einstein">
            Imagination is more important than knowledge.
        </Quote>

        <Quote author="Unknown">
            You are enough, just as you are.
        </Quote>
    </div>
</main>
