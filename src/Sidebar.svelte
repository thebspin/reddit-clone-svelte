<script>
  import { onMount } from 'svelte'
  import { Link } from 'svelte-routing'
  import { categories } from './store'

  let cats = []
  let filtered = [];
  let search = '';

  function sort(type) {
    filtered = filtered.sort((a, b) => {
      if (type === 'new') {
        return new Date(b.created).getTime() - new Date(a.created).getTime();
      }

      return new Date(a.created).getTime() - new Date(b.created).getTime();
    });
  }

  function filterCategories() {
    filtered = cats.filter(c => {
      if (c.name.toLowerCase().indexOf(search.toLowerCase()) > -1){
        return c;
      }
    })
  }

  onMount(async () => {
    const url = 'API_BASE_URL/category'
    const res = await fetch(url, {
      method: 'GET'
    })

    cats = await res.json()
    categories.set(cats)
    filtered = cats;
  })
</script>

<style>
  .sidebar {
    margin: 0 1rem;
    display: block;
    justify-content: space-between;
    background-color: #f9f9f9;
    width: 200px;
    padding: 0 1rem 1rem;
    height: calc(100vh - 6.8rem);
    width: 25rem;
    flex-shrink: 0;
    overflow-y: auto;
    position: sticky;
    top: 6.8rem;
  }

  .sidebar header {
    position: sticky;
    top: 0;
    padding-top: 1rem;
    background-color: #f9f9f9;
  }
  
  .sidebar header nav {
    padding: 1rem 0;
  }

  .sidebar ul{
    list-style: none;
    margin-bottom: 0px;
  }

  .sidebar input {
    background-color: #fff;
  }
  
  @media only screen and (max-width: 850px) {
    .sidebar {
      display: none;
    }
  }
</style>

<div class="sidebar">
  <header>
    <h3>Categories</h3>
    <input type="text" bind:value={search} on:keyup={filterCategories} />
    <nav>
      Sort: <a href="javascript:void(0)" on:click|preventDefault={() => sort('new')}>New</a>
      <a href="javascript:void(0)" on:click|preventDefault={() => sort('original')}>Original</a>
    </nav>
  </header>
  <ul>
    {#each filtered as category}
      <li>
        <Link to="/a/{ category.name }"><span>{ category.name }</span></Link>
      </li>
    {/each}
  </ul>
</div>