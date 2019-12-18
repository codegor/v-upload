<template>
  <div @drop.stop.prevent="drop" @dragenter.stop.prevent="setDrug" @dragleave.stop.prevent="offDrug"
    @dragover.stop.prevent="setDrug" :style="dropst">
    <slot :open="open"></slot>
    <input ref="file" type="file" :capture="capture" :multiple="multiple" :accept="accept"
      @change="newFile" style='display:none'/>
  </div>
</template>

<script>
  import each from 'lodash/each';

  export default {
    name: 'VUpload',
    components: {},
    filters: {},
    mixins: [],
    props: {
      value:    {},
      camera:   {type: Boolean, default: false},
      types:    {type: String, default: 'image/*'}, //application/pdf,application/vnd.ms-word,application/vnd.ms-excel
      multiple: {type: Boolean, default: false},
      clear: {type: Boolean, default: true}
    },
    data () {
      return {
        files: [],
        dropst: {}
      };
    },
    computed: {
      capture(){
        return this.camera ? 'camera' : '';
      },
      accept(){
        return this.camera ? 'image/*' : this.types;
      },
    },
    watch: {},
    created() {},
    mounted() {},
    methods: {
      drop(e){
        this.offDrug();
        // console.log(e.dataTransfer.files);
        this.newFile({target: e.dataTransfer});
      },
      setDrug(){
        this.dropst = {border: '1px dashed'};
      },
      offDrug(){
        this.dropst = {};
      },
      open(){
        this.$refs.file.click();
      },
      save(){
        let res;
        if(1 == this.files.length)
          res = this.files[0];
        else
          res = this.files;

        this.$emit('input', res);
        this.$emit('update', this.files);
      },

      newFile(e){
        // if(this.$debug) console.log('files to uploads', e.target.files);
        let res = [];
        if(this.clear)
          this.files = [];

        each(e.target.files, f => {
          res.push(new Promise((resolve, reject) => {
            let res = {}, fr = new FileReader(), fields = {};
            let parts = f.name.split('.');
            let type = parts.pop();
            fields.name = parts.join('.');
            fields.type = type;

            fr.readAsDataURL(f);
            fr.onload = (e) => {
              res.data = e.target.result.split(',')[1];
              res.size = f.size;
              res.name = f.name;
              res.type = type.toLocaleLowerCase();
              res.mime = f.type;
              fields.file = res;
              resolve();
            };
            fr.onerror = (e) => {reject();};
            this.files.push(fields);
          }));
        });

        Promise.all(res).then((e) => {
          this.save();
        });
      },
    },
  };
</script>

<style lang="scss">

</style>
