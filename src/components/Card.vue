<template>
  <div :class="type === 'reply' ? 'reply-container' : 'card-container'">
    <div class="card-elements-container">
      <div class="card-header-container">
        <div class="picture-container">
          <img
            class="picture"
            :src="require(`../assets/images/avatars/image-amyrobson.png`)"
            alt="profile-image"
          />
        </div>
        <div class="username-container">
          <span class="username">
            {{ id_user }}
          </span>
        </div>
        <div v-if="currentUser.username === id_user" class="you-label">
          <span>you</span>
        </div>
        <div class="createdAt-container">
          <span>
            {{ date }}
          </span>
        </div>
      </div>
      <div v-if="type === 'reply'" class="content-container">
        <span>@{{ replyingTo }} </span> {{ content }}
      </div>
      <div v-else class="content-container">
        {{ content }}
      </div>
      <div class="score-container">
        <div class="score-btn">
          <button
            @click="addScore()"
            :class="this.scoredUp ? 'score-add-btn scored' : 'score-add-btn'"
          >
            <img :src="plusIcon" alt="add-score" />
          </button>
          <span class="score-data">
            <span>{{ currentScore }}</span>
          </span>
          <button
            @click="minusScore()"
            :class="
              this.scoredDown ? 'score-minus-btn scored' : 'score-minus-btn'
            "
          >
            <img :src="minusIcon" alt="minus-score" />
          </button>
        </div>
      </div>
      <div v-if="currentUser.username === id_user" class="edit-container">
        <div
          @click="this.deleteModalOpen = !this.deleteModalOpen"
          class="delete"
        >
          <div>
            <img :src="deleteIcon" alt="delete comment" />
          </div>
          <span> Delete </span>
        </div>

        <div @click="this.replyFormOpen = !this.replyFormOpen" class="edit">
          <div>
            <img :src="editIcon" alt="edit comment" />
          </div>
          <span>Edit</span>
        </div>
      </div>
      <div
        @click="this.replyFormOpen = !this.replyFormOpen"
        v-else
        class="reply-btn-container"
      >
        <img class="reply-icon" :src="replyIcon" alt="reply icon" />
        <span>reply</span>
      </div>
    </div>
  </div>
  <CommentForm
    v-if="replyFormOpen"
    :threadId="threadId"
    :replyId="_id"
    :replyingTo="id_user"
    :commentFormType="commentFormType"
    :handleComment="handleComment"
    :currentUser="currentUser"
    :handleReplyFormOpen="handleReplyFormOpen"
    :content="content"
  />

  <div v-if="deleteModalOpen" class="delete-modal-container">
    <div class="delete-modal-form">
      <div class="delete-modal-title">Delete comment</div>
      <div class="delete-modal-info">
        Are you sure you want to delete this comment? This will remove the
        comment and can’t be undone.
      </div>
      <div class="delete-modal-btn">
        <button
          @click="this.deleteModalOpen = !this.deleteModalOpen"
          class="delete-cancel-btn"
        >
          NO, CANCEL
        </button>
        <button @click="handleDeleteConfirmation" class="delete-confirm-btn">
          YES, DELETE
        </button>
      </div>
    </div>
  </div>


</template>

<script>
import CommentForm from "../components/CommentForm.vue";
import PlusIcon from "../assets/images/icon-plus.svg";
import MinusIcon from "../assets/images/icon-minus.svg";
import ReplyIcon from "../assets/images/icon-reply.svg";
import DeleteIcon from "../assets/images/icon-delete.svg";
import EditIcon from "../assets/images/icon-edit.svg";
import axios from 'axios'

export default {
  name: "Card",
  components: {
    CommentForm,
  },
  
  props: {
    _id: String,
    content: String,
    date: String,
    score: Number,
    // optional
    replyingTo: String,
    id_user: String,
    // optional
    replies: Array,
    currentUser: Object,
    type: String,
    handleComment: Function,
    handleDeletion: Function,
    commentFormType: String,
    threadId: String,
  },
  created(){

console.log("currentUser.username")
console.log(this.currentUser.username)
console.log("id_user")
console.log(this.id_user)
},
  data: function () {
    return {
      scored: false,
      scoredUp: false,
      scoredDown: false,
      deleteModalOpen: false,
      replyFormOpen: false,
      currentScore: this.score,
      plusIcon: PlusIcon,
      minusIcon: MinusIcon,
      replyIcon: ReplyIcon,
      deleteIcon: DeleteIcon,
      editIcon: EditIcon,
      toDelete: {
        _id: this._id,
        threadId: this.threadId,
        type: this.type,
      },
    };
  },
  methods: {
    handleDeleteConfirmation() {

      console.log("this.type "+ this.type);
      axios.delete('http://localhost:3000/api/comments/comment/'+this.toDelete._id).then((response) => {
        console.log(response)
      })

      this.handleDeletion(this.toDelete);
      // this.deleteModalOpen = !this.deleteModalOpen;
    },
    handleReplyFormOpen() {
      this.replyFormOpen = !this.replyFormOpen;
    },
    addScore() {
      console.log("adding");
      if (!this.scored) {
        this.currentScore++;
        this.scored = true;
        this.scoredUp = true;
      } else if (this.scored && this.scoredUp) {
        this.currentScore--;
        this.scored = false;
        this.scoredUp = false;
      }
    },
    minusScore() {
      if (!this.scored) {
        this.currentScore--;
        this.scored = true;
        this.scoredDown = true;
      } else if (this.scored && this.scoredDown) {
        this.currentScore++;
        this.scored = false;
        this.scoredDown = false;
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.card-container {
  color: rgb(74, 23, 32);
  width: 100%;
  padding: 16px;
  background-color: var(--White);
  border-radius: 8px;
}
.reply-container {
  width: 94%;
  padding: 16px;
  background-color: var(--White);
  border-radius: 8px;
}

.card-elements-container {
  display: grid;
  grid-template-rows: 0.42fr 1.605fr 0.534fr;
  grid-template-columns: 1fr 1fr;
  gap: 16px;
}

.card-header-container {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  align-items: center;
  grid-column: span 2;
  gap: 16px;
}

.picture-container {
  display: flex;
  justify-content: center;
  align-items: center;
}
.picture {
  width: 32px;
  height: 32px;
}

.you-label {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 36px;
  height: 19px;
  background-color: var(--Moderate-blue);
  color: var(--White);
  border-radius: 2px;
}

.you-label > span {
  font-weight: 500;
  font-size: 13px;
}

.username {
  font-weight: 500;
  color: var(--Dark-blue);
}

.createdAt-container > span {
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 400;
  font-size: 16px;
  line-height: 24px;
  color: var(--Grayish-Blue);
}

.content-container {
  font-size: 16px;
  font-weight: 400;
  color: var(--Grayish-Blue);
  text-align: left;
  grid-column: span 2;
}

.content-container > span {
  color: var(--Moderate-blue);
  font-weight: 500;
}

.score-btn {
  width: 100px;
  height: 40px;
  border-radius: 10px;
  background-color: var(--Very-light-gray);
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}

.score-add-btn {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  border: none;
  background-color: var(--Very-light-gray);
  cursor: pointer;
}
.score-minus-btn {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  border: none;
  background-color: var(--Very-light-gray);
  cursor: pointer;
}

.scored {
  background-color: rgba(0, 0, 0, 0.25);
}

.score-data {
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 500;
  line-height: 18.96px;
  color: var(--Moderate-blue);
}

.edit-container {
  display: flex;
  justify-content: right;
  align-items: center;
  gap: 16px;
  font-weight: 700;
}
.edit-container .delete {
  display: flex;
  gap: 8px;
  cursor: pointer;
}
.edit-container .delete:hover {
  filter: opacity(25%);
}

.edit-container .delete > span {
  font-weight: 500;
  color: var(--Soft-Red);
}
.edit-container .edit {
  display: flex;
  gap: 8px;
  cursor: pointer;
}
.edit-container .edit:hover {
  filter: opacity(25%);
}
.edit-container .edit > span {
  font-weight: 500;

  color: var(--Moderate-blue);
}

.reply-btn-container {
  display: flex;
  justify-content: right;
  align-items: center;
  gap: 8px;
  font-weight: 700;
  color: var(--Moderate-blue);
  cursor: pointer;
}

.reply-btn-container:hover {
  filter: opacity(25%);
}

.delete-modal-container {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.7);
}

.delete-modal-form {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 16px;
  padding: 24px 27px 24px 27px;
  width: 343px;
  height: 224px;
  background-color: var(--White);
  text-align: center;
  border-radius: 8px;
}

.delete-modal-title {
  width: 100%;
  text-align: left;
  font-size: 20px;
  font-weight: 600;
  color: var(--Dark-blue);
}

.delete-modal-info {
  text-align: left;
  color: var(--Grayish-Blue);
}

.delete-modal-btn {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.delete-modal-btn > button:hover {
  filter: opacity(55%);
}

.delete-cancel-btn {
  border: none;
  width: 138px;
  height: 48px;
  background-color: var(--Grayish-Blue);
  color: var(--White);
  border-radius: 8px;
  font-weight: 600;
  font-size: 16px;
  cursor: pointer;
}
.delete-confirm-btn {
  border: none;
  width: 138px;
  height: 48px;
  background-color: var(--Soft-Red);
  color: var(--White);
  border-radius: 8px;
  font-weight: 600;
  font-size: 16px;
  cursor: pointer;
}
@media (min-width: 1440px) {
  .card-elements-container {
    grid-template-columns: 0.1967213115fr 1.7457fr 0.858fr;
    grid-template-rows: 0.474074074fr 1.0666666666fr;
  }

  .score-container {
    grid-row: 1 / 3;
  }

  .card-header-container {
    grid-column: 2;
  }

  .reply-btn-container {
    grid-row: 1;
    grid-column: 3;
  }
  .edit-container {
    grid-row: 1;
    grid-column: 3;
  }
  .content-container {
    grid-column: 2 / 4;
  }
  .score-btn {
    width: 40px;
    height: 100px;
    grid-template-columns: 1fr;
    grid-template-rows: 1fr 1fr 1fr;
  }
}
</style>
