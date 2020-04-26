---
layout: page
title: Buscar en este Blog
---
<div id="search-container">
<input type="text" id="search-input" placeholder="search...">
<ul id="results-container"></ul>
</div>

<!-- Script pointing to jekyll-search.js -->
<script src="/public/js/simple-jekyll-search.min.js" type="text/javascript"></script>
<script type="text/javascript">
SimpleJekyllSearch({
  searchInput: document.getElementById('search-input'),
  resultsContainer: document.getElementById('results-container'),
  json: '/search.json',
})
</script>