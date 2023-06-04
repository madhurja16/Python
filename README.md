# Python
Determination of coordination number of a metal using trajectory.

import os
import pandas as pd
import numpy as np

def big_f(fpath):
  df = pd.read_csv(fpath, sep = '\s+', header = None, names = ['n','x','y','z'])
  no_of_atoms = int(df.n[0])
  no_of_frames = len(df)//(no_of_atoms + 2)
  result = []
  for i in range(no_of_frames):
    result.append(df.iloc[i*(no_of_atoms+2)+2:(i+1)*(no_of_atoms+2)])
  return result
res = big_f("file")

# Function to calculate Euclidean distance
def euclidean_distance(x1, y1, z1, x2, y2, z2):
    return np.sqrt((x2 - x1)**2 + (y2 - y1)**2 + (z2 - z1)**2)

test=0
with open("name.txt", "w") as f:
# Function to calculate X metal distances in a dataframe
 def calculate_li_distances(df, list_of_atoms = ['O', 'F'], threshold = 2.2):
    li_rows = df[df['n'] == 'X'] # Filter rows with 'X'
    Li_number=0 #Added
    print("Co-ordination number of Li for below frame", file=f) #Added
    for index, row in li_rows.iterrows():
         test=0 #Added
         li_coords = row[[1, 2, 3]].values                              # Extract x, y, z values for Li
         li_name = f'X_{index}'                                        # Create a unique name for the Li atom
         df[li_name] = np.nan                                           # Create a new column with NaN values for storing distances
         for i, r in df.iterrows():
             if i != index:                                             # Skip the row for Li itself
               if r['n'] in list_of_atoms:
                 atom_coords = r[[1, 2, 3]].values                      # Extract x, y, z values for other atoms
                 distance = euclidean_distance(li_coords[0], li_coords[1], li_coords[2], atom_coords[0], atom_coords[1], atom_coords[2]) # Calculate Euclidean distance
                 if distance <= threshold:
                   test=test+1                                          # Added
                   df.at[i, li_name] = distance                         # Store the distance in the corresponding column
         Li_number=Li_number+1                                          # Added
         print("Li{}_CN".format(Li_number),test, file=f)                #Added
    return df.dropna(thresh=5)

 count = 0
 for df in res:
    df = calculate_li_distances(df)
    #print(df, file=f)
    count += 1
    print('frame',count, file=f)
    print('-------------------------------------------------------------', file=f)
