<template>
  <h1> </h1>
  <main class="main-container">
    <div class="comments-container">
      <Comment
        v-for="comment in comments"
        :key="comment._id"
        :currentUser="currentUser"
        :handleComment="handleComment"
        :handleDeletion="handleDeletion"
        v-bind:comment="comment"
      />
    </div>
    <CommentForm
      commentFormType="comment-new"
      :handleComment="handleComment"
      :currentUser="this.currentUser"
      :handleCommentDB="handleCommentDB"
    />
  </main>
</template>

<script>
import json from "../data.json";
import Comment from "./views/Comment.vue";
import CommentForm from "./components/CommentForm.vue";
import axios from 'axios'

export default {
  name: "App",
  components: {
    Comment,
    CommentForm,
  },
  data: () => {
    return {
      Generate_ID: 5,
      currentUser: json.currentUser,
      comments2: json.comments,
      comments: [],

    };
  },
  created(){
     axios.get('http://localhost:3000/api/comments').then((response) => {
        console.log(response.data)
        this.comments = response.data;
      })
  }
  ,
  methods: {
 
      
    handleDeletion(info) {
      if (info.type === "comment") {
        for (let i = 0; i < this.comments.length; i++) {
          if (this.comments[i]._id === info._id) {
            this.comments.splice(i, 1);
            break;
          }
        }
      } else if (info.type === "reply") {
        for (let i = 0; i < this.comments.length; i++) {
          if (this.comments[i]._id === info.threadId) {
            for (let j = 0; j < this.comments[i].replies.length; j++) {
              if (this.comments[i].replies[j]._id === info._id) {
                this.comments[i].replies.splice(j, 1);
                console.log(this.comments[i].replies);
                break;
              }
            }
          }
        }
      }
    },
    handleCommentDB(comment)
    {
      axios.post('http://localhost:3000/api/comments/comment', comment).then((response) => {
        console.log(response.data)
      });
    },
    handleComment(comment) {

      axios.post('http://localhost:3000/api/comments/comment', comment).then((response) => {
        console.log(response.data)
      });
      
      const commentType = comment.type;
      switch (commentType) {
        case "comment-new":

          this.comments.push({

            // _id: comment._id,
            // content: comment.commentInput,
            // createdAt: "Today",
            // score: 0,
            // user: comment.id_user,
            // replies: [],

            _id: comment._id,
            IdCommentedRessource: comment.IdCommentedRessource,
        TypeCommentedRessource:comment.TypeCommentedRessource,
        id_user: this.currentUser.username.id_user,
        content: comment.commentInput,
        IdCommentReplied: comment.IdCommentReplied


          });
          break;
        case "comment-reply":
          for (const c of this.comments) {
            if (c._id === comment.threadId) {
              c.replies.push({
                _id: this.Generate_ID++,
                content: comment.commentInput,
                createdAt: "Today",
                score: 0,
                replyingTo: comment.replyingTo,
                user: this.currentUser,
              });
            }
          }
          console.log(this.comments);
          break;
        case "reply-reply":
          for (const c of this.comments) {
            if (c._id === comment.threadId) {
              c.replies.push({
                _id: this.Generate_ID++,
                content: comment.commentInput,
                createdAt: "Today",
                score: 0,
                replyingTo: comment.replyingTo,
                user: this.currentUser,
              });
            }
          }
          break;

        case "comment-update":
          for (let i = 0; i < this.comments.length; i++) {
            if (this.comments[i]._id === comment.threadId) {
              this.comments[i].content = comment.commentInput;
              console.log(this.comments[i]);
              break;
            }
          }
          break;

        case "reply-update":
          for (let i = 0; i < this.comments.length; i++) {
            if (this.comments[i]._id === comment.threadId) {
              for (let j = 0; j < this.comments[i].replies.length; j++) {
                if (this.comments[i].replies[j]._id === comment.replyId) {
                  this.comments[i].replies[j].content = comment.commentInput;
                  break;
                }
              }
            }
          }

          break;
        default:
          console.log("no case");
          console.log(comment);
      }
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Rubik:wght@400;500;700&display=swap");

:root {
  --Moderate-blue: hsl(238, 40%, 52%);
  --Soft-Red: hsl(358, 79%, 66%);
  --Light-grayish-blue: hsl(239, 57%, 85%);
  --Pale-red: hsl(357, 100%, 86%);

  --Dark-blue: hsl(212, 24%, 26%);
  --Grayish-Blue: hsl(211, 10%, 45%);
  --Light-gray: hsl(223, 19%, 93%);
  --Very-light-gray: hsl(228, 33%, 97%);
  --White: hsl(0, 0%, 100%);

  --Mobile-breakpoint: 375px;
  --Desktop-breakpoint: 1440px;
}

html,
body {
  font-family: "Rubik", sans-serif;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-size: 16px;
}

.main-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 4% 2% 4% 2%;
}

.comments-container {
  width: 100%;
}
.comments-container > div:not(:first-child) {
  margin-top: 16px;
}

@media (min-width: 1440px) {
  .comments-container {
    width: 730px;
  }
}

#app {
  font-family: "Rubik", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: var(--Very-light-gray);
}
</style>
