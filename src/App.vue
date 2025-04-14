<template>
  <main>

    <section>
      <h2>
        What's up, <input type="text" id="name" placeholder="Name here">
      </h2>
    </section>

    <section>
      <h3>CREATE A TODO</h3>

      <form>
        <h4>What's on your todo list?</h4>
        <input type="text" name="content" id="content" placeholder="e.g. make a video" />

        <h4>Pick a category</h4>
        <div>

          <label>
            <input type="radio" name="category" id="category1" value="business" />
            <!-- <span></span> -->
            <div>Business</div>
          </label>

          <label>
            <input type="radio" name="category" id="category2" value="personal" />
            <!-- <span></span> -->
            <div>Personal</div>
          </label>

        </div>

        <input type="submit" />
      </form>
    </section>

    <section>
      <h3>TODO LIST</h3>
      <div>

        <div>
          <label>
            <input type="checkbox" />
          </label>

          <div>
            <input type="text" />
          </div>

          <div>
            <button>Delete</button>
          </div>
        </div>

      </div>
    </section>

  </main>
</template>


<script>
export default {
  name: 'App',
};
</script>

<style scoped></style>