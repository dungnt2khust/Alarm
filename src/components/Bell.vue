<template>
  <div class="bell">
    <div class="bell__overlay"></div>
    <div class="bell__main">
      <div class="bell__image">
        <img
          :src="imgs[Math.round(Math.random() * (imgs.length - 1))]"
          alt="Bell"
        />
      </div>
      <div class="bell__message">{{ message }}</div>
      <div class="bell__message">{{ time }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Bell",
  props: {
    message: {
      type: String,
      default: "",
    },
    time: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      imgs: [
        "https://media.giphy.com/media/sHV6YMsVFTQD6/giphy.gif",
        "https://media.giphy.com/media/3orifhxb3O7EcS9Uxa/giphy.gif",
        "https://media.giphy.com/media/W22mILIPv9WtQl7ttd/giphy.gif",
        "https://media.giphy.com/media/3otPozYJOVpoLI35ja/giphy.gif",
        "https://media.giphy.com/media/KEkSfpDtG1HtEPDYSt/giphy.gif",
        "https://media.giphy.com/media/KVofRUfF8AUGmwNVoL/giphy.gif",
        "https://media.giphy.com/media/A3fATyWcTO3oS98jvJ/giphy.gif",
        "https://media.giphy.com/media/3Z11sCBiALCEyX9YUk/giphy.gif",
        "https://media.giphy.com/media/mCE6MxgATI9auSBcs3/giphy.gif",
      ],
    };
  },
};
</script>

<style lang="scss">
.bell {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  transition: 0.3s all ease;
  .bell__overlay {
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    transition: 0.3s all ease;
  }
  .bell__main {
    position: absolute;
    transform: translate(-50%, -50%);
    left: 50%;
    top: 50%;
    width: 500px;
    background: #fff;
    border-radius: 20px;
    transition: 0.3s all ease;
    display: flex;
    flex-direction: column;
    padding: 10px;
    justify-content: center;
    align-items: center;
    .bell__image {
    }
    .bell__message {
      margin-top: 10px;
      font-weight: bold;
    }
  }
}
</style>