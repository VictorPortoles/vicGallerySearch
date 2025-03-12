import os
import shutil
from PIL import Image, ImageTk, UnidentifiedImageError
import tkinter as tk
from tkinter import filedialog, messagebox, ttk
import logging
import pillow_avif  # Import the plugin for AVIF support

# Configure the log file
logging.basicConfig(filename='error.log', level=logging.ERROR)

def open_folder():
    directory = filedialog.askdirectory()
    if directory:
        display_images(directory)

def move_image(image_path, label, VicGallerySearch_dir, count_label, moved_label):
    try:
        if not os.path.exists(VicGallerySearch_dir):
            os.makedirs(VicGallerySearch_dir)
        shutil.move(image_path, VicGallerySearch_dir)
        label.grid_forget()
        count_label.config(text=f"Images found: {int(count_label.cget('text').split(': ')[1]) - 1}")
        moved_label.config(text=f"Images moved: {int(moved_label.cget('text').split(': ')[1]) + 1}")
    except Exception as e:
        logging.error(f"Error moving the image: {e}")
        messagebox.showerror("Error", "Could not move the image.")

def move_all_images(VicGallerySearch_dir):
    target_directory = filedialog.askdirectory()
    if target_directory:
        try:
            for filename in os.listdir(VicGallerySearch_dir):
                file_path = os.path.join(VicGallerySearch_dir, filename)
                if os.path.isfile(file_path):
                    shutil.move(file_path, target_directory)
            messagebox.showinfo("Success", "All images have been moved successfully.")
        except Exception as e:
            logging.error(f"Error moving all images: {e}")
            messagebox.showerror("Error", "Could not move all images.")

def display_images(directory):
    result_window = tk.Toplevel(root)
    result_window.title("Images in Folder")
    result_window.configure(bg='gray')  # Set background to gray

    canvas = tk.Canvas(result_window, bg='gray')  # Set background to gray
    scrollbar = ttk.Scrollbar(result_window, orient="vertical", command=canvas.yview)
    scrollable_frame = ttk.Frame(canvas)

    scrollable_frame.bind(
        "<Configure>",
        lambda e: canvas.configure(
            scrollregion=canvas.bbox("all")
        )
    )

    canvas.create_window((0, 0), window=scrollable_frame, anchor="nw")
    canvas.configure(yscrollcommand=scrollbar.set)

    VicGallerySearch_dir = os.path.join(directory, 'VicGallerySearch')

    move_all_button = ttk.Button(result_window, text="Move All To", command=lambda: move_all_images(VicGallerySearch_dir))
    move_all_button.pack(side="top", anchor="ne", padx=10, pady=10)

    row, col = 0, 0
    image_count = 0
    for root_dir, _, files in os.walk(directory):
        for filename in files:
            if filename.endswith(('.png', '.jpg', '.jpeg', '.avif', '.webp')):
                img_path = os.path.join(root_dir, filename)
                try:
                    img = Image.open(img_path)
                    img.thumbnail((150, 150))  # Thumbnail size
                    img = ImageTk.PhotoImage(img)
                    label = ttk.Label(scrollable_frame, image=img)
                    label.image = img
                    label.grid(row=row, column=col, padx=5, pady=5)
                    label.bind("<Button-1>", lambda e, path=img_path, lbl=label: move_image(path, lbl, VicGallerySearch_dir, count_label, moved_label))
                    col += 1
                    if col == 10:  # Number of columns
                        col = 0
                        row += 1
                    image_count += 1
                except Exception as e:
                    logging.error(f"Error displaying the image: {e}")
        if not search_subdirs_var.get():
            break

    canvas.pack(side="left", fill="both", expand=True)
    scrollbar.pack(side="right", fill="y")

    # Display the number of images found
    global count_label
    count_label = ttk.Label(result_window, text=f"Images found: {image_count}")
    count_label.pack(side="bottom", anchor="e", padx=10, pady=5)

    # Display the number of images moved
    global moved_label
    moved_label = ttk.Label(result_window, text="Images moved: 0")
    moved_label.pack(side="bottom", anchor="e", padx=10, pady=5)

# Create the GUI
root = tk.Tk()
root.title("Image Gallery")

frame = ttk.Frame(root)
frame.pack(pady=20)

btn_open_folder = ttk.Button(frame, text="Select Folder", command=open_folder)
btn_open_folder.pack(pady=10)

global search_subdirs_var
search_subdirs_var = tk.IntVar(value=0)
btn_toggle = ttk.Checkbutton(frame, text="Include Subdirectories", variable=search_subdirs_var)
btn_toggle.pack(pady=10)

root.mainloop()
