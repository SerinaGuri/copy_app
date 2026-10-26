
<script>
    import Quote from '#lib/components/Quote.svelte';
    import { isPointWithinRadius, getDistance } from 'geolib';

    let status = $state('Visit your school to unlock an exclusive quote.');
    let unlocked = $state(false);
    let loading = $state(false);
    let distance = $state(null);

    const SCHOOL = {
        latitude: 42.05910523134263,
        longitude: 19.517268301553923
    };

    // Allowed distance from the school center, in meters.
    const RADIUS = 30;

    function checkLocation() {
        if (!navigator.geolocation) {
            status = 'Geolocation is not supported by your browser.';
            return;
        }

        loading = true;
        status = 'Checking your location...';

        navigator.geolocation.getCurrentPosition(
            (position) => {
                const userLocation = {
                    latitude: position.coords.latitude,
                    longitude: position.coords.longitude
                };

                const schoolLocation = {
                    latitude: SCHOOL.latitude,
                    longitude: SCHOOL.longitude
                };

                const accuracy = position.coords.accuracy;

                distance = getDistance(
                    userLocation,
                    schoolLocation
                );

                const isAtSchool = isPointWithinRadius(
                    userLocation,
                    schoolLocation,
                    RADIUS
                );

                if (isAtSchool && accuracy <= RADIUS) {
                    unlocked = true;
                    status = 'Welcome! Your school quote is unlocked.';
                } else if (isAtSchool) {
                    status =
                        'Your GPS accuracy is too low. Try again outdoors.';
                } else {
                    status = `You are approximately ${distance} meters away from school. Get closer to unlock the quote!`;
                }

                loading = false;
            },
            (error) => {
                if (error.code === 1) {
                    status =
                        'Location permission denied. Please allow location access.';
                } else if (error.code === 3) {
                    status =
                        'Location request timed out. Please try again.';
                } else {
                    status =
                        'Unable to get your location. Please try again.';
                }

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

    <section
        class="mx-auto mb-8 max-w-5xl rounded-xl border border-pink-200 bg-white p-6 text-center shadow-sm"
    >
        <h2 class="mb-2 text-xl font-semibold text-gray-800">
            School-exclusive quote
        </h2>

        <p class="mb-4 text-gray-600" aria-live="polite">
            {status}
        </p>

        {#if distance !== null}
            <p class="mb-4 text-sm text-gray-500">
                Distance from school: {distance} meters
            </p>
        {/if}

        <button
            class="rounded-lg bg-pink-500 px-5 py-3 font-medium text-white transition hover:bg-pink-600 disabled:cursor-not-allowed disabled:opacity-50"
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

    <div
        class="mx-auto grid max-w-5xl grid-cols-1 gap-5 sm:grid-cols-2 lg:grid-cols-3"
    >
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
                <Quote author="Data Exchange — School Exclusive">
                    Every great journey begins with showing up. Keep learning,
                    keep growing, and believe in yourself.
                </Quote>
            </div>
        {/if}
    </div>
</main>
