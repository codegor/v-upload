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
      clear:    {type: Boolean, default: true},
      debug:    {type: Boolean, default: true},
      maxSize:  {type: Number, default: 0}, // Mb
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
      mimes(){
        return this.accept.split(',')
      }
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
      check(mime){
        let r = false;
        if('' == this.accept)
          return true;

        this.mimes.forEach(v => {
          if(v.includes('*')){
            let e = new RegExp(v.replace(/[-/.]/g, '\\$&').replace(/[*]/g, '.$&'))
            r = r ? r : e.test(mime);
          } else
            r = r ? r : v == mime;
        });
        return r;
      },

      newFile(e){
        // if(this.$debug) console.log('files to uploads', e.target.files);
        let res = [];
        let old = JSON.parse(JSON.stringify(this.files));
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

              let mimeCheck = this.check(res.mime);
              let sizeCheck = 0 != this.maxSize ? res.size <= this.maxSize*1024*1024 : true;

              if(mimeCheck && sizeCheck)
                this.files.push(fields);
              else {
                if(!mimeCheck){
                  if(this.debug)
                    console.error('v-upload cannot save file with mime '+res.mime+', because its not present in types', fields);
                  this.$emit('e_mime', res.mime);
                }
                if(!sizeCheck){
                  if(this.debug)
                    console.error('v-upload cannot save file '+res.name+', because its size is '+Math.round(res.size*100/1024/1024)/100+'Mb, but Max size is '+this.maxSize+'Mb', fields);
                  this.$emit('e_size', res.size);
                }
              }

              resolve();
            };
            fr.onerror = (e) => {reject();};
          }));
        });

        Promise.all(res).then((e) => {
          if(0 == this.files.length)
            this.files = old;
          this.save();
        });
      },
    },
  };
</script>

<style lang="scss">

</style>
