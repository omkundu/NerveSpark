<!-- HeroSectionPage1.svelte -->
<script>
  import { onMount } from "svelte";
  import { writable } from "svelte/store";

  // Create a writable store to hold the ItemID
  const itemIDStore = writable(null);

  // Create a writable store to hold the selected item data
  const selectedItem = writable(null);

  // Subscribe to changes in the itemIDStore
  const unsubscribe = itemIDStore.subscribe(async (value) => {
    if (value) {
      const itemData = await fetchItemData(value);
      selectedItem.set(itemData); // Update the selectedItem store with the fetched data
    } else {
      selectedItem.set(null); // Reset the selectedItem when ItemID is null
    }
  });

  async function fetchItemData(ItemID) {
    try {
      const response = await fetch(`https://cars-mock-api-wjnb.onrender.com/cars/${ItemID}`);
      if (response.ok) {
        return await response.json();
      } else {
        console.error("Failed to fetch item data:", response);
        return null;
      }
    } catch (error) {
      console.error("Error fetching item data:", error);
      return null;
    }
  }

  onMount(() => {
    // Get the ID from the URL parameter and update the itemIDStore
    const ItemID = window.location.hash.replace("#/", "");
    itemIDStore.set(ItemID);
  });
</script>

<div>
  {#if $selectedItem}
    <h2>{$selectedItem.brand}</h2>
    <p>Price: ₹ {$selectedItem.Price}</p>
    <img src={$selectedItem.image} alt={$selectedItem.brand} />
    <!-- Display other details of the selected item here -->
  {/if}
</div>
