<template>
  <div class="comment-container">
    <Card
      :_id="comment._id"
      :content="comment.content"
      :date="comment.date"
      :id_user="comment.id_user"
      :currentUser="currentUser"
      :handleComment="handleComment"
      :handleDeletion="handleDeletion"
      type="comment"
      :threadId="comment._id"
      :commentFormType="
        currentUser.username === comment.id_user
          ? 'comment-update'
          : 'comment-reply'
      "
    />
    <!-- <div class="replies-container" v-if="comment.replies.length > 0">
      <Card
        v-for="reply in comment.replies"
        :key="reply._id"
        :_id="reply._id"
        :content="reply.content"
        :date="reply.date"
        :replyingTo="reply.replyingTo"
        :score="reply.score"
        :id_user="reply.id_user"
        :currentUser="currentUser"
        :handleComment="handleComment"
        :handleDeletion="handleDeletion"
        :threadId="comment._id"
        :commentFormType="
          currentUser.username === reply.id_user.username
            ? 'reply-update'
            : 'reply-reply'
        "
        type="reply"
      />
    </div> -->
  </div>
</template>

<script>
import Card from "../components/Card.vue";
export default {
  name: "Comment",
  components: {
    Card,
  },
  props: {
    currentUser: Object,
    comment: Object,
    handleComment: Function,
    handleDeletion: Function,
  },
  created(){

    console.log("test comment comment.vue")
    console.log(this.comment)
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.comment-container {
  color: rgb(74, 23, 32);
  width: 100%;
}

.replies-container {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  margin-top: 16px;
  border-left: 2px solid var(--Light-gray);
}

.replies-container > :not(:first-child) {
  margin-top: 16px;
}

@media (min-width: 1440px) {
  .comment-container {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
  }
  .replies-container {
    width: 685px;
  }
}
</style>
