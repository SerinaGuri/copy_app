
<script>
    import Quote from '#lib/components/Quote.svelte';

    let status = $state('Click the button to check your location.');
    let unlocked = $state(false);
    let distance = $state(null);
    let loading = $state(false);

    // Replace these with your school's coordinates.
    const SCHOOL = {
        latitude: 42.05907363826252, 
        longitude: 19.517295799266908
    };

    // Allowed distance from the school center, in meters.
    const RADIUS = 100;

    function getDistance(lat1, lon1, lat2, lon2) {
        const toRad = (degrees) => degrees * Math.PI / 180;
        const earthRadius = 6371000;

        const dLat = toRad(lat2 - lat1);
        const dLon = toRad(lon2 - lon1);

        const a =
            Math.sin(dLat / 2) ** 2 +
            Math.cos(toRad(lat1)) *
            Math.cos(toRad(lat2)) *
            Math.sin(dLon / 2) ** 2;

        return 2 * earthRadius *
            Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    }

    function checkLocation() {
        if (!navigator.geolocation) {
            status = 'Geolocation is not supported by this browser.';
            return;
        }

        loading = true;
        status = 'Checking your location...';

        navigator.geolocation.getCurrentPosition(
            (position) => {
                const { latitude, longitude, accuracy } =
                    position.coords;

                distance = Math.round(
                    getDistance(
                        latitude,
                        longitude,
                        SCHOOL.latitude,
                        SCHOOL.longitude
                    )
                );

                if (distance <= RADIUS && accuracy <= RADIUS) {
                    unlocked = true;
                    status = 'Welcome! Your school quote is unlocked.';
                } else {
                    status = distance <= RADIUS
                        ? 'Your location is not accurate enough. Try again outdoors.'
                        : `You are approximately ${distance} meters away. Get closer to your school!`;
                }

                loading = false;
            },
            (error) => {
                status = error.code === 1
                    ? 'Please allow location access in your browser.'
                    : 'Could not get your location. Please try again.';

                loading = false;
            },
            {
                enableHighAccuracy: true,
                timeout: 15000,
                maximumAge: 0
            }
        );
    }
</script>

<svelte:head>
    <title>Data Exchange</title>
    <meta
        name="description"
        content="A collection of quotes and inspiration."
    />
</svelte:head>

<main class="min-h-screen bg-gray-50 px-4 py-10 sm:px-8">
    <h1 class="mb-3 text-center text-3xl font-bold text-gray-800 sm:text-4xl">
        Data Exchange
    </h1>

    <p class="mb-8 text-center text-gray-500">
        A collection of quotes and inspiration.
    </p>

    <section class="mx-auto mb-8 max-w-5xl rounded-xl border bg-white p-6 text-center shadow-sm">
        <h2 class="mb-2 text-xl font-semibold text-gray-800">
            School-exclusive quote
        </h2>

        <p class="mb-4 text-gray-600" aria-live="polite">
            {status}
        </p>

        <button
            class="rounded-lg bg-pink-500 px-5 py-3 font-medium text-white hover:bg-pink-600 disabled:opacity-50"
            type="button"
            onclick={checkLocation}
            disabled={loading || unlocked}
        >
            {loading
                ? 'Checking location...'
                : unlocked
                    ? 'Quote unlocked!'
                    : 'Check my location'}
        </button>
    </section>

    <div class="mx-auto grid max-w-5xl grid-cols-1 gap-5 sm:grid-cols-2 lg:grid-cols-3">
        <Quote author="Serina Guri">
            Don't be Captain Obvious.
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

        {#if unlocked}
            <div class="sm:col-span-2 lg:col-span-3">
                <Quote author="A message from your school">
                    Every great journey begins with showing up.
                    Keep learning, keep growing, and believe in yourself.
                </Quote>
            </div>
        {/if}
    </div>
</main>
