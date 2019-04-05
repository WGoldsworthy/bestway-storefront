<template>
  <div class="top-header">
    <div class="container">
      <div class="top-header-left">
        <ul id="link-list-left">
          <li>Home</li>
          <li>Category Advice</li>
          <li>Services</li>
          <li>Our Brands</li>
          <li>About Us</li>
          <li>News</li>
          <li>Brochures</li>
        </ul>
      </div>
      <div class="top-header-right">
        <ul id="link-list-right">
          <li>Retailer Tools <img src="/../../assets/gridicon.png" height="14px"></li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {

}
</script>

<style scoped>
  .top-header {
    display: inline-block;
    background-color: #f2f2f2;
    width: 100%;
    padding: 5px;
    margin-bottom: 5px;
  }
  .top-header-left {
    float: left;
  }
  li {
    list-style-type: none;
    display: inline-block;
    padding: 10px;
    font-size: 0.7em;
  }
  li:hover {
    text-decoration-line: underline;
    cursor: pointer;
  }
  .top-header-right {
    float: right;
  }
  ul {
    margin: 0px;
    padding-inline-start: 0;
  }
</style>
