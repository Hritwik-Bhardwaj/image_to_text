# image_to_text
A c++  Program  to convert image into text
This wiki provides simple examples on how to use the tesseract-ocr API (v3.02.02-4.0.0) in C++. It is expected that tesseract-ocr is correctly installed including all dependecies. It is expected the user is familiar with C++, compiling and linking program on their platform, though basic compilation examples are included for beginners with Linux.

More details about tesseract-ocr API can be found at baseapi.
APIExample
zdenop edited this page on Oct 31, 2018 · 16 revisions
API examples

This wiki provides simple examples on how to use the tesseract-ocr API (v3.02.02-4.0.0) in C++. It is expected that tesseract-ocr is correctly installed including all dependecies. It is expected the user is familiar with C++, compiling and linking program on their platform, though basic compilation examples are included for beginners with Linux.

More details about tesseract-ocr API can be found at baseapi.h.
Basic example
APIExample
zdenop edited this page on Oct 31, 2018 · 16 revisions
API examples

This wiki provides simple examples on how to use the tesseract-ocr API (v3.02.02-4.0.0) in C++. It is expected that tesseract-ocr is correctly installed including all dependecies. It is expected the user is familiar with C++, compiling and linking program on their platform, though basic compilation examples are included for beginners with Linux.

More details about tesseract-ocr API can be found at baseapi.h.
Basic example

Code:

#include <tesseract/baseapi.h>
#include <leptonica/allheaders.h>

int main()
{
    char *outText;

    tesseract::TessBaseAPI *api = new tesseract::TessBaseAPI();
    // Initialize tesseract-ocr with English, without specifying tessdata path
    if (api->Init(NULL, "eng")) {
        fprintf(stderr, "Could not initialize tesseract.\n");
        exit(1);
    }

    // Open input image with leptonica library
    Pix *image = pixRead("/usr/src/tesseract/testing/phototest.tif");
    api->SetImage(image);
    // Get OCR result
    outText = api->GetUTF8Text();
    printf("OCR output:\n%s", outText);

    // Destroy used object and release memory
    api->End();
    delete [] outText;
    pixDestroy(&image);

    return 0;
}

The program must be linked to the tesseract-ocr and leptonica libraries.

If you want to restrict recognition to a sub-rectangle of the image - call SetRectangle(left, top, width, height) after SetImage. Each SetRectangle clears the recogntion results so multiple rectangles can be recognized with the same image. E.g.

  api->SetRectangle(30, 86, 590, 100);

GetComponentImages example
  Pix *image = pixRead("/usr/src/tesseract/testing/phototest.tif");
  tesseract::TessBaseAPI *api = new tesseract::TessBaseAPI();
  api->Init(NULL, "eng");
  api->SetImage(image);
  Boxa* boxes = api->GetComponentImages(tesseract::RIL_TEXTLINE, true, NULL, NULL);
  printf("Found %d textline image components.\n", boxes->n);
  for (int i = 0; i < boxes->n; i++) {
    BOX* box = boxaGetBox(boxes, i, L_CLONE);
    api->SetRectangle(box->x, box->y, box->w, box->h);
    char* ocrResult = api->GetUTF8Text();
    int conf = api->MeanTextConf();
    fprintf(stdout, "Box[%d]: x=%d, y=%d, w=%d, h=%d, confidence: %d, text: %s",
                    i, box->x, box->y, box->w, box->h, conf, ocrResult);
  }

Result iterator example

It is possible to get confidence value and BoundingBox per word from a ResultIterator:

  Pix *image = pixRead("/usr/src/tesseract/testing/phototest.tif");
  tesseract::TessBaseAPI *api = new tesseract::TessBaseAPI();
  api->Init(NULL, "eng");
  api->SetImage(image);
  api->Recognize(0);
  tesseract::ResultIterator* ri = api->GetIterator();
  tesseract::PageIteratorLevel level = tesseract::RIL_WORD;
  if (ri != 0) {
    do {
      const char* word = ri->GetUTF8Text(level);
      float conf = ri->Confidence(level);
      int x1, y1, x2, y2;
      ri->BoundingBox(level, &x1, &y1, &x2, &y2);
      printf("word: '%s';  \tconf: %.2f; BoundingBox: %d,%d,%d,%d;\n",
               word, conf, x1, y1, x2, y2);
      delete[] word;
    } while (ri->Next(level));
  }

It is also possible to use other iterator levels (block, line, word, etc.), see PageiteratorLevel.

Code:

#include <tesseract/baseapi.h>
#include <leptonica/allheaders.h>

int main()
{
    char *outText;

    tesseract::TessBaseAPI *api = new tesseract::TessBaseAPI();
    // Initialize tesseract-ocr with English, without specifying tessdata path
    if (api->Init(NULL, "eng")) {
        fprintf(stderr, "Could not initialize tesseract.\n");
        exit(1);
    }

    // Open input image with leptonica library
    Pix *image = pixRead("/usr/src/tesseract/testing/phototest.tif");
    api->SetImage(image);
    // Get OCR result
    outText = api->GetUTF8Text();
    printf("OCR output:\n%s", outText);

    // Destroy used object and release memory
    api->End();
    delete [] outText;
    pixDestroy(&image);

    return 0;
}

The program must be linked to the tesseract-ocr and leptonica libraries.

If you want to restrict recognition to a sub-rectangle of the image - call SetRectangle(left, top, width, height) after SetImage. Each SetRectangle clears the recogntion results so multiple rectangles can be recognized with the same image. E.g.

  api->SetRectangle(30, 86, 590, 100);

GetComponentImages example

  Pix *image = pixRead("/usr/src/tesseract/testing/phototest.tif");
  tesseract::TessBaseAPI *api = new tesseract::TessBaseAPI();
  api->Init(NULL, "eng");
  api->SetImage(image);
  Boxa* boxes = api->GetComponentImages(tesseract::RIL_TEXTLINE, true, NULL, NULL);
  printf("Found %d textline image components.\n", boxes->n);
  for (int i = 0; i < boxes->n; i++) {
    BOX* box = boxaGetBox(boxes, i, L_CLONE);
    api->SetRectangle(box->x, box->y, box->w, box->h);
    char* ocrResult = api->GetUTF8Text();
    int conf = api->MeanTextConf();
    fprintf(stdout, "Box[%d]: x=%d, y=%d, w=%d, h=%d, confidence: %d, text: %s",
                    i, box->x, box->y, box->w, box->h, conf, ocrResult);
  }

Result iterator example

It is possible to get confidence value and BoundingBox per word from a ResultIterator:

  Pix *image = pixRead("/usr/src/tesseract/testing/phototest.tif");
  tesseract::TessBaseAPI *api = new tesseract::TessBaseAPI();
  api->Init(NULL, "eng");
  api->SetImage(image);
  api->Recognize(0);
  tesseract::ResultIterator* ri = api->GetIterator();
  tesseract::PageIteratorLevel level = tesseract::RIL_WORD;
  if (ri != 0) {
    do {
      const char* word = ri->GetUTF8Text(level);
      float conf = ri->Confidence(level);
      int x1, y1, x2, y2;
      ri->BoundingBox(level, &x1, &y1, &x2, &y2);
      printf("word: '%s';  \tconf: %.2f; BoundingBox: %d,%d,%d,%d;\n",
               word, conf, x1, y1, x2, y2);
      delete[] word;
    } while (ri->Next(level));

