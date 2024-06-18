<template>
  <div class="item">
    <div class="details">
      <h2>
        <slot name="score"></slot>
      </h2>
      <h3>
        <slot name="heading"></slot>
      </h3>
      <slot name="options"></slot>
      <slot></slot>
    </div>
  </div>
</template>

<style scoped>
.item {
  margin-top: 2rem;
  display: flex;
  position: relative;
  font-family: 'Lato', sans-serif;
}

.details {
  flex: 1;
  margin-left: 1rem;
}

h3 {
  font-size: 1.1rem;
  font-weight: 500;
  margin-bottom: 0.4rem;
  color: var(--color-heading);
  font-style: italic;
  letter-spacing: 0.03rem;
  line-height: 1.5;
}

h2 {
  font-size: 1.2rem;
  font-weight: 700;
  margin-bottom: 0.4rem;
  color: var(--color-heading);
  text-transform: uppercase;
  letter-spacing: 2px;
  line-height: 1.5;
}

@media (min-width: 1024px) {
  h2 {
    font-size: 1.5rem;
  }

  h3 {
    font-size: 1.2rem;
  }
  .item {
    margin-top: 0;
    padding: 0.4rem 0 1rem calc(var(--section-gap) / 2);
  }

  .item:before {
    content: ' ';
    position: absolute;
    left: 0;
    bottom: calc(50% + 25px);
    height: calc(50% - 25px);
  }

  .item:after {
    content: ' ';
    position: absolute;
    left: 0;
    top: calc(50% + 25px);
    height: calc(50% - 25px);
  }

  .item:first-of-type:before {
    display: none;
  }

  .item:last-of-type:after {
    display: none;
  }
}
</style>
