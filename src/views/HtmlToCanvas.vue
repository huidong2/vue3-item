<template>
  <main>
    <div style="height: 100%;width: 100%">
        <div  id="pdfDom" style="visibility: hidden;">
            <div style="width:100px;height:50px;border:1px solid black;box-sizing: border-box; padding: 5px;" >123</div>
        </div>
    </div>
  </main>
</template>
<script setup lang="ts">
import html2canvas from 'html2canvas'
import JsPDF from 'jspdf'
import { nextTick, onBeforeMount, onMounted } from 'vue';

onMounted(()=>{
  nextTick(()=>{
    html2canvas(document.getElementById('pdfDom'), {
      allowTaint: true,
      useCORS: true,
      dpi: window.devicePixelRatio * 4, // 将分辨率提高到特定的DPI 提高四倍
      scale: 4,
      // scale: 2, // 提升画面质量，但是会增加文件大小
      // 需要注意，element的 高度 宽度一定要在这里定义一下，不然会存在只下载了当前你能看到的页面   避雷避雷！！！
      height: document.getElementById('pdfDom').scrollHeight,
      windowHeight: document.getElementById('pdfDom').scrollHeight,
      onclone: cb => {
        cb.getElementById('pdfDom').style.visibility = 'visible'
    }
    })
      .then((canvas) => {
        let contentWidth = canvas.width;
        let contentHeight = canvas.height;
        let imgWidth = 841.89;
        // var imgWidth = 595.28;
        let imgHeight = imgWidth / contentWidth * contentHeight;
        let imageUrl = canvas.toDataURL('image/png'); // 将canvas转成base64图片格式
        // let pdf = new JsPDF('l', 'pt', 'a4');
        // pdf.addImage(imageUrl, 'JPEG', 0, 0, imgWidth, imgHeight, '');
        // pdf.save('test.pdf');
        let a = document.createElement('a')
		a.style.display = 'none'
		let blob = dataURLToBlob(canvas.toDataURL('image/png'))
		a.setAttribute('href',URL.createObjectURL(blob))
		a.setAttribute('download','test.png')
		document.body.appendChild(a)
		a.click()
		URL.revokeObjectURL(blob)
		document.body.removeChild(a)
      });
  })
})

const dataURLToBlob = function(dataUrl){
	let arr = dataUrl.split(','),
		mime = arr[0].match(/:(.*?);/)[1],
		bstr = atob(arr[1]),
		n = bstr.length,
		u8arr = new Uint8Array(n)
	while(n--){
		u8arr[n] = bstr.charCodeAt(n)
	}
	return new Blob([u8arr],{type:mime})
}
</script>

