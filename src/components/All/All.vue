/** ============================================
table of contents
================================================

1. Display All Categories
2. Call Api using Apollo Client
3.Scss Code


*/





/* *=======================================
1. Display All Categories
*========================================== */



<template>
    <div v-if="loading">
        <loading />
    </div>
    <div v-else-if="error">Error: {{ error.message }}</div>
    <div v-else class="flex flex-wrap justify-center">
        <div v-for="category in categories" :key="category.id">
            <div
                class="relative card m-10 flex w-full max-w-xs flex-col overflow-hidden rounded-lg border border-gray-100 bg-white shadow-md">
                <a class="relative mx-3 mt-3 flex h-60 overflow-hidden rounded-xl" href="#">
                    <div v-if="category.image">
                        <img loading="lazy" class="object-cover w-full" :src="category.image"
                            :alt="(category.name || '').split(' ').slice(0, 2).join(' ')" @error="handleImageError" />
                    </div>


                    <div v-else>
                        <img loading="lazy" class="object-cover imgDefault w-full" :src="imgDefault"
                            alt="Default Image" />
                    </div>
                </a>
                <div class="mt-4 px-5 pb-5">
                    <h5 class="text-xl tracking-tight text-slate-900">
                        {{ (category.name || '').split(' ').slice(0, 3).join(' ') }}
                    </h5>


                </div>
            </div>
        </div>
    </div>
</template>



/* *=======================================
2. Call Api using Apollo Client
*========================================== */




<script lang="ts">
import { useQuery } from '@vue/apollo-composable';
import { ref, watchEffect } from 'vue';
import gql from 'graphql-tag';
import imgDefault from '../../assets/istockphoto-1409329028-612x612.jpg';
import loading from '../../components/Loading/Loading.vue';

export default {
    components: {
        loading
    },

    setup() {

        const GET_CATEGORIES = gql`
        query {
          categories {
            id
            name
            image
          }
        }
        `;

        const { result, error, loading } = useQuery(GET_CATEGORIES);
        const categories = ref([]);

        // Handle image error (fallback to default image)
        const handleImageError = (event: Event) => {
            const img = event.target as HTMLImageElement;
            if (img.src !== imgDefault) {
                console.log('Image error, loading default image.');
                img.src = imgDefault;
            }
        };

        // Watch the result and update categories
        watchEffect(() => {
            if (result.value) {
                categories.value = result.value.categories || [];
                console.log('Categories:', categories.value);
            }
        });

        return {
            categories,
            error,
            loading,
            result,
            handleImageError,
            imgDefault
        };
    }
};
</script>


/* *=======================================
3. Scss Code
*========================================== */



<style lang="scss" scoped>
.card {
    margin: 10px;

    img {
        transition: all 0.3s ease-in-out;

        &:hover {
            transform: scale(1.1);
            filter: brightness(0.9);
        }
    }
}
</style>
