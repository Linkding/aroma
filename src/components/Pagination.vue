<template>
    <div class="pagination">
        <span class="btn-group">
      <button class="btn-small"
              :disabled="is_first_page"
              @click="change(1)">首
      </button>
      <button class="btn-small"
              :disabled="is_first_page"
              @click="prev()">前
      </button>
    </span>

    &nbsp;

    <span class="btn-group">
                <button v-if="Math.abs(page - current_page) <= half_limit"
                        @click="change(page)"
                        :class="{active: current_page == page}"
                        v-for="page in last_page"
                        class="btn-small"
                        :key="page"
                        >{{page}}
                        
                </button>
              </span>
    &nbsp;

    <span class="btn-group">
      <button class="btn-small"
              :disabled="is_last_page"
              @click="next()">下
      </button>

      <button class="btn-small"
              :disabled="is_last_page"
              @click="change(last_page)">末
      </button>
    </span>
    </div>
</template>
<script>
export default {
  props: {
    limit: {
      default: 0
    },
    totalCount: {
      default: 0
    },
    onChange: {
      default() {
        console.log(1);
      }
    },
    currentPage: {
      default: 1
    }
  },
  data() {
    return {
      current_page: 1
    };
  },
  methods: {
    change(page) {
      if (this.onChange) {
        this.onChange(page);
      }
      this.current_page = page;
    },
    prev() {
      if (this.is_first_page) return;
      this.change(--this.current_page);
    },
    next() {
      if (this.is_last_page) return;
      this.change(++this.current_page);
    }
  },
  computed: {
    half_limit() {
      return Math.floor(this.limit / 2);
    },
    is_first_page() {
      return this.current_page == 1;
    },
    is_last_page() {
      return this.current_page == this.last_page;
    },
    last_page() {
      return Math.ceil(this.totalCount / this.limit);
    }
  },
  watch: {
    currentPage(n) {
      this.current_page = n;
    }
  }
};
</script>
<style scoped>
.pagination {
  text-align: center;
  padding: 10px 0;
}
.pagination .active {
  background: #f9726c;
}
</style>
