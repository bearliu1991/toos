<template>
    <div>
        <a href="http://localhost:3001/download/postcard" download="postcard">download</a> 
        <img id="confirm" src="" alt="">
        <div id="postcard" >
            <h1>hello</h1>
            <img id="postcard-img" src="" style="height:200px;width: 400px" crossorigin="anonymous">
        </div>
        <div>
            <button @click="createImg">create canvas img</button>
        </div>
        <button @click="submit">submit</button>
    </div>
</template>

<script>
import axios from 'axios'
import html2canvas from 'html2canvas';
export default {
    name: "upload",
    data() {
        return {
            form: '',
            ext: ''
        }
    },
    mounted(){
        // this.createBase64();
        this.createbase64forIE();
    },
    methods: {
        createImg() {
            html2canvas(document.querySelector("#postcard")).then(canvas => {
                document.body.appendChild(canvas)
                const dataURL = canvas.toDataURL("image/"+this.ext);
                this.createForm(dataURL)
                this.submit()
            });
        },
        createBase64(){
            var img = "https://ss0.baidu.com/-Po3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=9e71881537f33a87816d061af65c1018/8d5494eef01f3a2966bddeeb9425bc315c607c99.jpg";//imgurl 就是你的图片路径  

            const getBase64Image = (img) =>{  
                var canvas = document.createElement("canvas");
                canvas.width = img.width;  
                canvas.height = img.height;  
                var ctx = canvas.getContext("2d");  
                    ctx.drawImage(img, 0, 0, img.width, img.height);  
                this.ext = img.src.substring(img.src.lastIndexOf(".")+1).toLowerCase();  
                var dataURL = canvas.toDataURL("image/"+ this.ext);
                return dataURL;  
            }

            var image = new Image();
            image.crossOrigin = "Anonymous";
            image.onload = () => {
                var base64 = getBase64Image(image);
                document.querySelector("#postcard-img").setAttribute('src', base64)
            }
            image.src = img;
        },
        createbase64forIE() {
            var url = "https://ss0.baidu.com/-Po3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=9e71881537f33a87816d061af65c1018/8d5494eef01f3a2966bddeeb9425bc315c607c99.jpg";//imgurl 就是你的图片路径  

            var self = this;
            const getBase64Image = (img) =>{  
                var canvas = document.createElement("canvas");
                canvas.width = img.width;  
                canvas.height = img.height;  
                var ctx = canvas.getContext("2d");  
                    ctx.drawImage(img, 0, 0, img.width, img.height);  
                this.ext = img.src.substring(img.src.lastIndexOf(".")+1).toLowerCase();  
                var dataURL = canvas.toDataURL("image/"+ this.ext);
                return dataURL;
            }

            var xhr = new XMLHttpRequest();
            xhr.onload = function () {
                var url = URL.createObjectURL(this.response), img = new Image();
                img.onload = function () {
                    // here you can use img for drawing to canvas and handling
                    var base64 = getBase64Image(img);
                    document.querySelector("#postcard-img").setAttribute('src', base64)
                    // don't forget to free memory up when you're done (you can do this as soon as image is drawn to canvas)
                    URL.revokeObjectURL(url);
                };
                img.src = url;
            };
            xhr.open('GET', url, true);
            xhr.responseType = 'blob';
            xhr.send();
        },
        createForm(base64) {
            this.form = new FormData();
            this.form.append("image", base64);
            return 
        },
        submit() {
            axios.post('http://localhost:3001/uploadpaste', this.form).then(res => {
                document.querySelector("#confirm").setAttribute('src', "http://localhost:3001/"+ res.data
.store_path)
            })
            return false
        }
    }
}
</script>

<style>
    #postcard {
        position: relative;
        height: 200px;
    }
    #postcard h1{
        position: absolute;
        top: 0;
        left: 0
    }
    #postcard-img{
        position: absolute;
        top: 0;
        left: 200px;
    }
</style>


