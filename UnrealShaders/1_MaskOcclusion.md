# Mask Occlusion - 2 Methods
![mygif](./MaskOcclusion.gif)

## Method 1 - Standalone Material

<img width="544" height="474" alt="image" src="https://github.com/user-attachments/assets/4f9ce241-993d-4728-ad58-bdc34c5f061c" />



### 1. Create a Material, set it to Mask and add this node network

<img width="1276" height="450" alt="image" src="https://github.com/user-attachments/assets/64330860-f13d-4e1f-818e-c2e7ecbcb872" />

### But if you need the ceiling to also be a floor  
_for example if you can walk on the roof_  

Add the bottom section.
<img width="1452" height="898" alt="image" src="https://github.com/user-attachments/assets/fff82410-ab2e-48ef-945a-34cda88292bb" />

## Method 2 - Fade Ceiling with a Trigger

<img width="535" height="395" alt="image" src="https://github.com/user-attachments/assets/156a7620-33b7-4ad5-a2f8-169758cc6c48" />


1. Create a Material Parameter Collection in the Content Drawer
- Add a Scalar Parameter called `Alpha`  
<img width="105" height="174" alt="image" src="https://github.com/user-attachments/assets/6a69c146-b900-4a70-acc6-f114bf16c797" />

<img width="534" height="453" alt="image" src="https://github.com/user-attachments/assets/b1759474-6574-44aa-8bb7-43a323b5aa3a" />

2. Create a Material
- Drag your Material Parameter collection into the Material Graph
- Click on the Collection Parameter node and set it to `Alpha`
- Add a DitherTemporalAA Node
- Set the Material type to Mask, and plug these nodes in:  
<img width="696" height="195" alt="image" src="https://github.com/user-attachments/assets/53927875-df6c-463d-886e-ccb055e3df8e" />

3. Create a Blueprint with a mesh and a trigger box.  
<img width="341" height="164" alt="image" src="https://github.com/user-attachments/assets/09a38ed1-d50d-4f7a-b8a8-374091d17ab0" />

- Then Create this Graph using trigger overlaps and Timelines

<img width="888" height="493" alt="image" src="https://github.com/user-attachments/assets/28bd7f62-70d2-4d85-a9a8-45a305ecffa7" />

_This is how the timeline looks, it will need a track, and two keys from 1 to 0_
<img width="752" height="373" alt="image" src="https://github.com/user-attachments/assets/ebc838db-9811-4bbc-b1af-9db707438445" />

_Make sure you set the Scalar Parameter Value options_

<img width="240" height="231" alt="image" src="https://github.com/user-attachments/assets/6e7aeca2-ed5a-45d8-ae8f-30d523829330" />
