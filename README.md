const { jsPDF } = window.jspdf;
let cropper;

// TAB SWITCH
function showTab(id) {
  document.querySelectorAll(".card").forEach(c => c.classList.add("hidden"));
  document.getElementById(id).classList.remove("hidden");
}

// IMAGE ➜ PDF
function imageToPDF() {
  const file = document.getElementById("imgInput").files[0];
  if (!file) return alert("Select an image");

  const reader = new FileReader();
  reader.onload = e => {
    const img = new Image();
    img.src = e.target.result;
    img.onload = () => {
      const pdf = new jsPDF();
      pdf.addImage(img, "JPEG", 10, 10, 190, 0);
      pdf.save("image.pdf");
    };
  };
  reader.readAsDataURL(file);
}

// PDF ➜ IMAGE
async function pdfToImage() {
  const file = document.getElementById("pdfInput").files[0];
  if (!file) return alert("Select a PDF");

  const data = await file.arrayBuffer();
  const pdf = await pdfjsLib.getDocument({ data }).promise;
  const page = await pdf.getPage(1);

  const viewport = page.getViewport({ scale: 2 });
  const canvas = document.getElementById("pdfCanvas");
  const ctx = canvas.getContext("2d");

  canvas.width = viewport.width;
  canvas.height = viewport.height;

  await page.render({ canvasContext: ctx, viewport }).promise;
}

// IMAGE CROP
document.getElementById("cropInput").addEventListener("change", e => {
  const img = document.getElementById("cropImage");
  img.src = URL.createObjectURL(e.target.files[0]);

  img.onload = () => {
    if (cropper) cropper.destroy();
    cropper = new Cropper(img, { viewMode: 1 });
  };
});

function doCrop() {
  const canvas = cropper.getCroppedCanvas();
  document.getElementById("cropResult").innerHTML = "";
  document.getElementById("cropResult").appendChild(canvas);
}
