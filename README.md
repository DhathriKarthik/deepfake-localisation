**PixelGuard is a deepfake detection and localization system for face images.**

The system takes a face photo and decides whether it is real or fake. If it is fake, PixelGuard also highlights the specific region of the image that led to that decision, rather than just giving a plain yes or no answer. 
It is built as a working web application, with a FastAPI backend and a browser-based frontend, rather than just a training notebook.
Detection works by combining three separate models, each specialized in a different kind of fake. 
One model is trained to recognize fully GAN-generated faces, a second is a fine-tuned version of the same model trained additionally on images from a different GAN source to improve generalization, and a third is trained to recognize face-swap deepfakes using the Celeb-DF v2 dataset. 
All three models share the same EfficientNet-B0 architecture, but were trained on different data. 
When an image is checked, all three models give their own fake-probability score, and the highest of the three scores is taken as the final result. 
This way, the system does not need to know in advance what kind of fake it might be looking at.
If the image is classified as fake, the system generates a heatmap using a technique called EigenCAM, which shows which parts of the image most influenced the model's decision. 
This heatmap is cleaned up using thresholding and basic image processing steps, then restricted to the area where a face is expected to be, producing a clear highlighted region over the manipulated area. 
The final output includes the original image, the raw heatmap, a cleaned binary mask, and the image with the manipulated region highlighted in red.
The project also went through real iteration rather than working perfectly on the first attempt. 
The original model reached 96% accuracy on its own test set, but performed poorly on face images from a different GAN source, revealing a generalization gap. 
This was addressed by fine-tuning the model on a small set of images from that source, which improved accuracy on the new source significantly, though it came at the cost of some accuracy loss on the original test set. 
This trade-off, along with its causes, is documented openly rather than hidden, since understanding why a system behaves a certain way is just as important as the final numbers.
All models were trained on Kaggle using GPU acceleration, since local hardware was not capable of GPU-based training. Training notebooks, evaluation scripts, and the dataset collection script used for fine-tuning are all kept in the notebooks folder for reference and reproducibility.
The current scope of the project is limited to face images and to two manipulation techniques, GAN synthesis and face-swap. Extending the system to video, other manipulation techniques, and reducing the false positive rate introduced during fine-tuning are identified as natural next steps for future work.
