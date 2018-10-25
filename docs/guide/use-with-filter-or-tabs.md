---
previewLink: //jsfiddle.net/PeachScript/w197rfy0/embedded/result/
---

# Use With Filter/Tabs

The loading process is exactly same as the previous example, the key point is how to reset the component when we changing filter or tabs. In fact, this component will reset itself whenever the `identifier` is changed, sounds ease, let's do it!

``` html {12}
<header>
  <!-- Hacker News header -->
  <select v-model="newsType" @change="changeType">
    <!-- Type options -->
  </select>
</header>

<div v-for="(item, $index) in list" :key="$index">
  <!-- Hacker News item loop -->
</div>

<infinite-loading :identifier="infiniteId" @infinite="infiniteHandler"></infinite-loading>
```

In the template, we add a `select` element and listen it's `change` event, for `InfiniteLoading` component, we add an `identifier` property.

``` js {10,11,19,31,32,33,34,35}
import axios from 'axios';

const api = '//hn.algolia.com/api/v1/search_by_date';

export default {
  data() {
    return {
      page: 1,
      list: [],
      newsType: 'story',
      infiniteId: +new Date(),
    };
  },
  methods: {
    infiniteHandler($state) {
      axios.get(api, {
        params: {
          page: this.page,
          tags: this.newsType,
        },
      }).then(({ data }) => {
        if (data.hits.length) {
          this.page += 1;
          this.list.push(...data.hits);
          $state.loaded();
        } else {
          $state.complete();
        }
      });
    },
    changeType() {
      this.page = 1;
      this.list = [];
      this.infiniteId += 1;
    },
  },
};
```

In the script, we set default values for `select` and `identifier` property, then add the type parameter in API request logic. And we create the `changeType` method to reset the list data and infinite component, please pay attention, we must change the `identifier` property after empty list, if not, the component may not trigger the `infinite` event immediately after reset.

That's all, you have done it!