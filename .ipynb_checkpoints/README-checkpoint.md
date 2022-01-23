# Smart Shopping Cart - POC
<left><img src="POCCart_Image1.jpg" alt="Header" style="float: center; width: 400px;"/>
<img src="TitleImageTBD.jpg" alt="Header" style="width: 400px;"/></left>

<br>This is **Jetson Nano 2GB** based project developed as a proof of concept to demonstrate usage of **AI (Image Classification)**, for implementing a "Smart Shopping Cart" which identify the produts placed inside the cart and invoice shopper accordingly. **Eliminating lengthy queues and improving shopping experience** is the objective of this project.
<font size="1"><br>※3D Image Credit : https://www.eedesignit.com/video-the-future-of-grocery-shopping-lies-in-the-smart-cart/caper/</font>

<br>Software Requirement : **JetPack 4.6** (tested with)

# Introduction
This project have two modes :
1. **Shop Manage Mode** : This is where supermarket staff can add new products to store. 
    For simplicity I have supported only **product name & price** and that information is stored in "ProductMaster.csv".
    In addition, staff can upload an advertising image of the product (could be a 3D rendering),
    which will be shown in preview window once Jetson identify actual product using USB camera.
    
    <br><left><img src="AddProductImage.png" alt="Header" style="float: center; width: 300px;"/></left>
    
    Once products are added, staff have an interface to train the model. I use already partly trained **ResNet-18** model for this.
    Staff can campture training images using same same hardware, train and save the model. 
    I have uploaded a couple of common sample products like a *Can of Coke* along with this project.
    
    <br><left><img src="TrainingImage.png" alt="Header" style="float: center; width: 400px;"/></left>
    
2. **Shopping Mode** : In this mode, a real time camera preview is displayed on left side of the screen so that shopper can see what is being analyzed and
    an advertising image of identified products on the right side. Also I display a list of products placed in the cart, their individual prices and
    total sum at the bottom (the "Invoice").
    
    <br><left><img src="TestImage2.png" alt="Header" style="float: center; width: 400px;"/></left>

# An Ideal Usecase
Long queues at the cashier for payment is one of the biggest dissapointment when it comes to shopping. Also from management point of view, 
allocating staff during peak hours to man the cashiers demand a lot of planing and increase operational expenses. Tech companies like [Amazon Go](https://en.wikipedia.org/wiki/Amazon_Go) utilize a great deal of technology to solve this issue. What I conceptualize here is a **vision base system
which identify products put in the card and prepair the invoice accrodingly**. An ideal usecase would be as follows:
1. Smart shopping cart is equipped with a display, NFC reader, receipt printer, tiny computer like Jetson + Wifi module
1. Shopper tap NFC supported credit/debit card to start shopping
2. Add required products to shopping cart
3. Cart identify the products and update sales invoice in real time
4. At the end of shopping, shopper confirm the invoice for payment
5. Shopping card debit the shopper, issue invoice and update supermarket backend

# My Setup
For this POC, I set up everything on a plastic shopping card. Waveshare 7" display fit nicely and powering everything using portable battery was very convenient. If somebody is attempting the same, I recommend using a bit bigger cart because camera can not capture some large items in full due to short distance.

<br><left><img src="Setup.jpg" alt="Header" style="float: center; width: 800px;"/></left>

## List of Hardware
- Jetson Nano 2GB
- Logitec C270 HD USB Web Camera
- 64GB Micro SD Card
- [Waveshare 7inch Capacitive Touch Screen](https://www.waveshare.com/product/displays/lcd-oled/lcd-oled-1/7inch-hdmi-lcd-h-with-case.htm)
- [HIDISC 20000mAh 18W Power Bank](https://www.amazon.co.jp/HIDISC-QC3-0%E5%AF%BE%E5%BF%9C-20000mAh%E3%83%A2%E3%83%90%E3%82%A4%E3%83%AB%E3%83%90%E3%83%83%E3%83%86%E3%83%AA%E3%83%BC-%E3%83%9B%E3%83%AF%E3%82%A4%E3%83%88-HD-MBPD18W20000BTWH/dp/B089QJ8MVS)
- Cabels for power, SSH Connection & Video (USB-C, Micro USB, HDMI)
    
# Software Configuration & Running the Demo

1. Assuming Jetson nano initial setup is already done (best place to visit is [DLI cource page](https://courses.nvidia.com/courses/course-v1:DLI+S-RX-02+V2/about)), easiest way to replicate is by creating a folder named **shopping_cart** inside **nvdli-nano/data/** and copy entire content of this git repository to there.

2. Click Run -> Run all cells and first page (Home) will have two buttons. First one (Shop Manage) is to register products and train the network. Second button (Start Shopping) is where products are detected using trained network.
<br><left><img src="HomeImage.png" alt="Header" style="float: center; width: 800px;"/></left>

3. Click on Shop Manage and add the product you like. Enter product name & price here and select an image of the product. This image is not used for training, but used to display when the product is identified in Shopping Mode (uploading nice marketing image here is recommended). Click save and product data will be saved to *ProductMaster.csv* file. Display image is saved to *display_image* folder.

4. Click start training & select the product from dropdown list. Default data set is "Primary" and number of already captured images is displayed in Count. Go ahead and add capture some images of the product you registered. I found small items like *Sparkling Water* need more training images for accurate prediction. After that, select number of Epochs for training (I found about 10 Epochs is enough for most cases) and hit Train. Be patient as it may take 2-3 min for first epoch to run and display the progress. As training progress observe Losses get reduced and Accuracy get improved.
<br><left><img src="TrainingImage2.png" alt="Header" style="float: center; width: 400px;"/></left>

5. Finally click **Save Model** and **Start Shopping** to check how it works.

## Video
[![Smart Shopping Cart](https://img.youtube.com/vi/KTV-JgeoOC0/0.jpg)](https://www.youtube.com/watch?v=KTV-JgeoOC0)

## Performance
1. Traning time with 5 items (20 images each) for 10 epoch take around 250 seconds
2. First image detection time is around 35 seconds (needs improvement)
3. I have limited the analysed farems per sec. 1 as it is enough for this application. 
4. Observed a performance drop when connected to external display (non headless mode).
   <br>My guess is Ubuntu chrome browser require a considerable amount of resources (need some debugging)

# Limitations & Todo's
1. This simple POC does not support adding same product mutiple times
2. Image classification alone is not enough in real world. Activity recognision (eg. using Tensor RT) is required to detect if the product is put in or taken out of the cart
3. It would be nice to see if somebody can integrate a receipt printer with Jetson
4. Integrate NFC card reader with Jetson also would be a nice touch
5. Need to run training everytime after restart