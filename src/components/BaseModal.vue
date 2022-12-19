<template>
    <Transition name="modal-out">
        <div class="absolute w-full bg-black bg-opacity-50 h-screen top-0 left-0 flex justify-center px-8"
        @click.self="$emit('closeModal')">
        <Transition name="modal-in">
            <div class=" p-4 bg-white self-start mt-32 max-w-screen-md">
                <slot />
                <button class="text-white mt-8 bg-weather-primary py-2 px-6"
                @click="$emit('closeModal')">
                    Close
                </button>
            </div>
        </Transition>
        </div>
    </Transition>
</template>

<script setup>
    defineEmits(['closeModal'])

</script>

<style>

    .modal-out-enter-active,
    .modal-out-leave-active {
        transition: all 2s ease;
    }

    .modal-out-enter-from,
    .modal-out-leave-to {
        opacity: 0;
    }

    

</style>