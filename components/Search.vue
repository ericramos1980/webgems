<template lang="pug">
  input.search(v-model="searchInput" type="text" placeholder="Search")
</template>

<script>
import * as R from 'ramda'
import { isNotEmpty } from '../utils/pure'

export default {
  data() {
    return {
      searchInput: '',
      searchPath: '/search',
    }
  },
  methods: {
    // isTag :: String -> Bool
    isTag: R.startsWith('#'),

    // removeFirstChar :: String -> String
    removeFirstChar: R.compose(
      R.join(''),
      R.adjust(0, () => '')
    ),
  },
  watch: {
    searchInput(input) {
      const words = R.filter(isNotEmpty, R.split(' ', input))
      const tags = R.filter(this.isTag, words)
      const titles = R.filter(R.compose(R.not, this.isTag), words)

      const searchParams = new URLSearchParams()
      if (isNotEmpty(titles))
        searchParams.append('keywords', titles)
      if (isNotEmpty(tags))  
        searchParams.append('tags', R.map(this.removeFirstChar, tags))
      
      this.$router.push(this.searchPath + '?' + searchParams.toString())
    },
  },
}
</script>

<style lang="scss">
input {
  padding: .5rem 1.5rem .5rem 1.5rem;
  border-radius: .3rem;
  background: #eee;
  font-size:12px;

  &:focus {
    outline:none;
  }
}
</style>
