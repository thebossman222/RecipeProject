<script lang="ts">
    import { onMount } from "svelte";
    import { recipes } from "../stores/recipes"; // Ensure recipes have 'instructions' property instead of 'recipes'

    let searchQuery = '';
    let filteredRecipes: { id: number; title: string; description: string; ingredients: string; category: string; instructions: string;}[] = [];
    let newTitle = '';
    let newDescription = '';
    let newIngredients = '';
    let newRecipe = '';
    let newCategory = '';
    let selectedCategory = 'All';
    let categoryList: string[] = [];

    // Lifecycle function runs when the page loads
    onMount(() => {
        filteredRecipes = recipes;
        categoryList = [...new Set(recipes.map(recipe => recipe.category ? recipe.category.toLowerCase() : ''))];
    });

    // Search & Filter Function
    function handleSearch() {
        filteredRecipes = recipes.filter(recipe =>
            recipe.title.toLowerCase().includes(searchQuery.toLowerCase()) &&
            (selectedCategory === 'All' || (recipe.category ?? '').toLowerCase() === selectedCategory.toLowerCase())
        );
    }

    // Function to add a new recipe
    function addRecipe() {
        const newFood = {
            id: Date.now(),
            title: newTitle,
            description: newDescription,
            ingredients: newIngredients,  // Fixed typo
            category: newCategory.trim().toLowerCase(), // Normalizing case
            instructions: newRecipe, // Add an empty string or appropriate value for 'instructions'
        };

        recipes.push(newFood);
        filteredRecipes = recipes;

        if (!categoryList.includes(newFood.category)) {
            categoryList.push(newFood.category);
            categoryList = [...new Set(recipes.map(recipe => recipe.category ? recipe.category.toLowerCase() : ''))];
        }

        // Reset the form
        newTitle = '';
        newDescription = '';
        newIngredients = '';
        newCategory = '';
        newRecipe = '';
    }
</script>

<h1>Recipes</h1>

<!-- Search Bar -->
<input type="text" bind:value={searchQuery} placeholder="Search for a recipe" on:input={handleSearch} />

<!-- Category Filter -->
<select bind:value={selectedCategory} on:change={handleSearch}>
    <option value="All">All</option>
    {#each categoryList as category}
        <option value={category}>{category}</option>
    {/each}
</select>

<!-- Add a New Recipe Form -->
<h3>Add a New Recipe</h3>
<form on:submit|preventDefault={addRecipe}>
    <input type="text" placeholder="Recipe Title" bind:value={newTitle} required />
    <textarea placeholder="Recipe Description" bind:value={newDescription} required></textarea>
    <textarea placeholder="Ingredients (comma-separated)" bind:value={newIngredients} required></textarea>
    <input type="text" placeholder="Category" bind:value={newCategory} required />
    <input type="text" placeholder="Recipe" bind:value={newRecipe} required />
    <button type="submit">Add Food</button>
</form>

<!-- Display Recipes -->
<div>
    {#each filteredRecipes as recipe}
        <div>
            <a href="/{recipe.id}">
                <h2>{recipe.title}</h2>
            </a>
            <p>{recipe.description}</p>
            <p><strong>Ingredients:</strong> {recipe.ingredients}</p>
            <p><strong>Category:</strong> {recipe.category}</p>
        </div>
    {/each}
</div>
