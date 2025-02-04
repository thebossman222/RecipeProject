<script lang="ts">
    import { goto } from '$app/navigation';
    import { page } from '$app/stores';
    import { recipes } from '../../stores/recipes';
    import { onMount } from 'svelte';

    let recipe: {title: string, description: string, ingredients: string, category: string, instructions: string} | undefined; // Define the recipe object

    // Get the recipe ID from the URL
    let id: string;
    $: id = $page.params.id;

    onMount(() =>  {
        recipe = recipes.find((r) => r.id.toString() === id) ?? undefined;
    });

    function deleteRecipe() {
        const index = recipes.findIndex((r) => r.id.toString() === id);
        if(index !== -1) {
            recipes.splice(index, 1); // Remove the recipe from the array
            goto('/'); // Redirect back home!!!
        }
    }
</script>

{#if recipe}
<h1>Title</h1>
<p>{recipe.title}</p>
<h1>Description</h1>
<p>{recipe.description}</p>
<h1>Ingredients</h1>
<p>{recipe.ingredients}</p>
<h1>Category</h1>
<p>{recipe.category}</p>
<h1>Recipe</h1>
<p>{recipe.instructions}</p>


<button on:click={() => goto(`/${id}/edit`)}>Edit Recipe</button>
<button on:click={deleteRecipe}>Delete Recipe</button>
<button on:click={() => goto('/')}>Back to Home</button>
{:else}
<p>Recipe not found!</p>
{/if}