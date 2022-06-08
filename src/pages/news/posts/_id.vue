<template>
  <article class="article">
    <header class="article__head">
      <p class="article__head__date">
        {{ postData.modified | dateFilter }}
      </p>
      <h1 class="article__head__title">
        {{ postData.title.rendered }}
      </h1>
    </header>
    <main class="article__main">
      <figure v-if="postData._embedded['wp:featuredmedia']" class="article__main__thumbnail">
        <img :src="postData._embedded['wp:featuredmedia'][0].source_url" :alt="postData.title.rendered">
      </figure>
      <!-- eslint-disable-next-line vue/no-v-html -->
      <div class="article__main__content" v-html="postData.content.rendered" />
    </main>
  </article>
</template>

<script>
export default {
  name: 'NewsDetailPage',
  async asyncData ({ params, $config, $axios, error }) {
    // WP REST APIのベースURL
    const baseUrl = $config.wpBaseUrl
    // お知らせの詳細記事を取得するためのエンドポイント作成
    const postDetailUrl = `${baseUrl}posts/${params.id}?_embed`
    // axiosを使って記事を取得
    const postData = await $axios
      .$get(postDetailUrl)
      .then((post) => {
        return post
      })
      .catch((e) => {
        // 取得できない場合はエラーページへ遷移
        error({
          statusCode: 404,
          message: 'not found'
        })
      })

    // template内で使えるようにreturnする
    return {
      postData
    }
  }
}
</script>

<style lang="scss" scoped>
.article {
  padding-top: 40px;
  &__head {
    &__date {
    }
    &__title {
      margin-top: 8px;
      font-size: 2.4rem;
    }
  }
  &__main {
    &__thumbnail {
      margin: 32px 0;
    }
    &__content {
      &::v-deep {
      > * {
        word-break: break-all;
        margin: 16px 0;
      }
      p {
        line-height: 1.8;
      }
    }
    }
  }
}
</style>