<template>




<form action="" method="post" @submit.prevent="handleSend"
    :class="[commentFormType === 'reply-reply'? 'comment-form-reply-container': 'comment-form-container',commentFormType === 'reply-update'? 'comment-form-reply-container':'comment-form-container']">

    <div class="input-container">
      <textarea
        v-model="commentData.commentInput"
        class="comment-input"
        name="content"
        placeholder="Add a comment..."
        @input="handleContentComment"

      >
      </textarea>

    </div>
    <div class="picture-container">
      <img
        class="picture"
        :src="require(`../assets/${this.currentUser.image.png}`)"
        alt="profile picture"
      />
    </div>
    <div class="send-btn-container">
      <button @click="handleReplyFormOpen ? handleReplyFormOpen() : null" class="send-btn" type="submit">
        <span v-if="commentFormType === 'comment-new'">SEND</span>
        <span v-if="commentFormType === 'comment-reply' ||commentFormType === 'reply-reply'">REPLY</span
        >
        <span v-if="commentFormType === 'comment-update' ||commentFormType === 'reply-update'">UPDATE</span
        >
      </button>
    </div>
  </form>
</template>
<script>
export default {
  name: "CommentForm",
  props: {
    replyId: String,
    currentUser: Object,
    handleComment: Function,
    commentFormType: String,
    replyingTo: String,
    threadId: String,
    handleReplyFormOpen: Function,
    content: String,
    handleCommentDB : Function
  },
  data: function () {
    return {
        currentContent: "",
        commentWrited: "",

        commentData: {

        type: this.commentFormType,
        commentInput: this.commentFormType === 'comment-update' || this.commentFormType === 'reply-update' ? this.content : "",
        TypeCommentedRessource:"ressources",
        id_user : this.currentUser.username,
        replyingTo: this.replyingTo,
        threadId: this.threadId,
        replyId: this.replyId,
      },
      
      commentDB : 
        {
        IdCommentedRessource: "63b5572459e5954ded196ada",
        TypeCommentedRessource:"ressources",
        id_user: this.currentUser.username,
        content: "",
        IdCommentReplied:null
      }
    };
  },
  methods: {

    handleContentComment(event) {

      console.log(event.target.value);
      this.commentWrited=event.target.value;
    },

    handleSend() {
      
      console.log(this.commentDB)
      this.commentDB.content=this.commentWrited;
      this.handleCommentDB(this.commentDB)

      this.handleComment(this.commentData)
      this.commentData.commentInput = ""
    },
  },
};
</script>
<style scoped>
.comment-form-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 0.5fr;
  gap: 16px;
  padding: 16px;
  margin-top: 16px;
  height: 189px;
  width: 100%;
  border-radius: 8px;
  background-color: var(--White);
}
.comment-form-reply-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 0.5fr;
  gap: 16px;
  padding: 16px;
  margin-top: 16px;
  width: 94%;
  height: 189px;
  border-radius: 8px;
  background-color: var(--White);
}

.input-container {
  grid-column: span 2;
}

.comment-input {
  padding: 12px 24px 12px 24px;
  width: 100%;
  height: 100%;
  border: 1px solid var(--Light-gray);
  border-radius: 8px;
  font-family: "Rubik", sans-serif;
  font-weight: 500;
  color: var(--Grayish-Blue);
}

.comment-input:focus {
  outline: none !important;

  border: 1px solid var(--Moderate-blue);
}

.picture-container {
  display: flex;
  align-items: center;
}

.picture {
  width: 32px;
  height: 32px;
}

.send-btn-container {
  display: flex;
  justify-content: flex-end;
  align-items: center;
}

.send-btn {
  height: 48px;
  width: 104px;
  border: none;
  border-radius: 8px;
  color: var(--White);
  background-color: var(--Moderate-blue);
  font-weight: 500;
  font-family: "Rubik", sans-serif;
}

.send-btn:hover {
  cursor: pointer;
  background-color: var(--Light-grayish-blue);
}
@media (min-width: 1440px) {
  .comment-form-container {
    grid-template-columns: 0.1719197708fr 2.1747851004fr 0.4469914041fr;
    grid-template-rows: 1fr;
    width: 730px;
  }

  .input-container {
    grid-row: 1;
    grid-column: 2;
  }

  .picture-container {
    align-items: flex-start;
    grid-column: 1;
  }
  .picture {
    width: 40px;
    height: 40px;
  }

  .send-btn-container {
    align-items: flex-start;
    grid-column: 3;
  }
}

</style>
