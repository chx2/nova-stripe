<template>
    <div class="bg-20 rounded-b">
        <nav v-if="resource.length > 0" class="flex">
            <!-- Previous Link -->
            <button
                    :disabled="!hasPrevious"
                    class="btn btn-link py-3 px-4"
                    :class="{
                    'text-primary dim': hasPrevious,
                    'text-80 opacity-50 cursor-not-allowed': !hasPrevious,
                }"
                    rel="prev"
                    @click.prevent="previousPage()"
                    dusk="previous"
            >
                {{__('Previous')}}
            </button>

            <!-- Next Link -->
            <button
                    :disabled="!hasMore"
                    class="ml-auto btn btn-link py-3 px-4"
                    :class="{
                    'text-primary dim': hasMore,
                    'text-80 opacity-50 cursor-not-allowed': !hasMore,
                }"
                    rel="next"
                    @click.prevent="nextPage()"
                    dusk="next"
            >
                {{__('Next')}}
            </button>
        </nav>
    </div>
</template>

<script>
    export default {
        props: ['resource', 'hasMore', 'hasPrevious'],

        methods: {
            /**
             * Select the previous page.
             */
            previousPage() {
                this.$emit('previous')
            },

            /**
             * Select the next page.
             */
            nextPage() {
                this.$emit('next')
            },
        },
    }
</script>
