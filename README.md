# VideoPlayer-Project
from tkinter import *
import tkinter.scrolledtext as tkst
import font_manager as fonts
import video_library as lib
from tkinter import messagebox


def set_text(text_area, content):
    text_area.delete("1.0", END)
    text_area.insert(1.0, content)


class SampleApp(Tk):

    def __init__(self, *args, **kwargs):
        Tk.__init__(self, *args, **kwargs)

        fonts.configure()

        container = Frame(self)
        container.pack(side="top", fill="both")
        container.grid_rowconfigure(0, weight=1)
        container.grid_columnconfigure(0, weight=1)

        self.frames = {}
        for F in (VideoMenu, CheckVideos, UpdateVideos, CreateVideoList):
            page_name = F.__name__
            frame = F(parent=container, controller=self)
            self.frames[page_name] = frame

            # put all of the pages in the same location;
            # the one on the top of the stacking order
            # will be the one that is visible.
            frame.grid(row=0, column=0, sticky="nsew")

        self.show_frame("VideoMenu")

    def show_frame(self, page_name):
        """Show a frame for the given page name"""
        frame = self.frames[page_name]
        frame.tkraise()


class VideoMenu(Frame):

    def __init__(self, parent, controller):
        Frame.__init__(self, parent, bg="#d2e0e0")
        self.controller = controller
        self.controller.title('Video Player')

        heading_label = Label(self, text="Video Player", font=("arial", 40, "bold"), fg="black", bg="#d2e0e0")
        heading_label.pack(pady=25)

        main_menu_lbl = Label(self, text="Main Menu", font=("arial", 25, "bold"), fg="black", bg="#d2e0e0")
        main_menu_lbl.pack()

        header_lbl1 = Label(self, text="Select one of the following options:", font=("arial", 15, "bold"), fg="black",
                            bg="#d2e0e0", anchor="w")
        header_lbl1.pack(fill="x")

        button_frame = Frame(self, bg="#c3d5d5")
        button_frame.pack(fill="both", expand=True)

        def Check():
            controller.show_frame("CheckVideos")

        check_videos_btn = Button(button_frame, text="Check Videos", width=50, command=Check, relief="raised",
                                  borderwidth=3, height=5, fg="black")
        check_videos_btn.grid(row=0, column=0, pady=10)

        def Update():
            controller.show_frame("UpdateVideos")

        update_btn = Button(button_frame, text="Update Videos", width=50, command=Update, relief="raised",
                            borderwidth=3, height=5, fg="black")
        update_btn.grid(row=1, column=0, pady=10)

        def Create():
            controller.show_frame("CreateVideoList")

        create_btn = Button(button_frame, text="Create Playlist", width=50, command=Create, relief="raised",
                            borderwidth=3, height=5, fg="black")
        create_btn.grid(row=2, column=0, pady=10)


class CheckVideos(Frame):
    def check_video_clicked(self):
        try:
            key = self.input_txt.get()
            name = lib.get_name(key)
            if name is not None:
                director = lib.get_director(key)
                rating = lib.get_rating(key)
                play_count = lib.get_play_count(key)
                video_details = f"{name}\n{director}\nrating: {rating}\nplays: {play_count}"
                set_text(self.video_txt, video_details)
            else:
                set_text(self.video_txt, f"Video {key} not found")
            self.status_lbl.configure(text="Check Video button was clicked!")
        except ValueError:
            messagebox.showinfo(f"Error: Please enter a valid integer")

    def list_videos_clicked(self):
        try:
            video_list = lib.list_all()
            set_text(self.list_txt, video_list)
            self.status_lbl.configure(text="List Videos button was clicked!")
        except AttributeError:
            messagebox.showinfo(f"Error: Sorry, could not list the videos")

    try:
        def __init__(self, parent, controller):
            Frame.__init__(self, parent, bg="#d2e0e0")
            self.controller = controller

            heading_label = Label(self, text="Video Player", font=("arial", 40, "bold"), fg="black",
                                  bg="#d2e0e0")
            heading_label.pack(pady=25)

            main_menu_lbl = Label(self, text="Check Videos Details", font=("arial", 15, "bold"), fg="black",
                                  bg="#d2e0e0")
            main_menu_lbl.pack()

            button_frame = Frame(self, bg="#c3d5d5")
            button_frame.pack(fill="both", expand=True)

            list_videos_btn = Button(button_frame, text="List All Videos", command=self.list_videos_clicked,
                                     relief="raised", borderwidth=3, height=2, width=30, fg="black")
            list_videos_btn.grid(row=0, column=0, padx=30, pady=10)

            enter_lbl = Label(button_frame, text="Enter Video Number:", font=("arial", 15), fg="black",
                              bg="#c3d5d5")
            enter_lbl.grid(row=0, column=2, padx=10, pady=10)

            self.input_txt = Entry(button_frame, width=5, fg="black", bg="#c3d5d5")
            self.input_txt.grid(row=0, column=3, padx=10, pady=10)

            check_video_btn = Button(button_frame, text="Check Video", command=self.check_video_clicked,
                                     relief="raised", borderwidth=3, height=2, width=30, fg="black")
            check_video_btn.grid(row=0, column=4, padx=30, pady=10)

            self.list_txt = tkst.ScrolledText(button_frame, width=62, height=22, wrap="none", fg="black", bg="#c3d5d5")
            self.list_txt.grid(row=1, column=0, columnspan=3, sticky="W", padx=10, pady=10)

            self.video_txt = Text(button_frame, width=42, height=12, wrap="none", fg="black", bg="#c3d5d5")
            self.video_txt.grid(row=1, column=4, sticky="NW", padx=10, pady=10)

            self.status_lbl = Label(button_frame, text="", font=("arial", 12), fg="black", bg="#c3d5d5")
            self.status_lbl.grid(row=2, column=0, columnspan=5, sticky="W", padx=10, pady=10)

            def back():
                controller.show_frame("VideoMenu")

            exit_button = Button(button_frame, font=("arial", 15), text="Back", command=back, relief="raised",
                                 borderwidth=3, width=15, height=1, fg="black")
            exit_button.grid(row=3, column=3, padx=30, pady=10)
    except AttributeError:
        messagebox.showinfo(f"Sorry, could not open the GUI")


class UpdateVideos(Frame):
    def update_video_clicked(self):
        key = self.input_txt.get()
        name = lib.get_name(key)
        if name is not None:
            rating = self.input_rating_txt.get()
            lib.set_rating(key, int(rating))
        else:
            set_text(self.video_txt, f"Video {key} not found")
        self.status_lbl.configure(text="Update Video button was clicked!")

    def check_video_clicked(self):
        key = self.input_txt.get()
        name = lib.get_name(key)
        if name is not None:
            director = lib.get_director(key)
            rating = lib.get_rating(key)
            play_count = lib.get_play_count(key)
            video_details = f"{name}\n{director}\nrating: {rating}\nplays: {play_count}"
            set_text(self.video_txt, video_details)
        else:
            set_text(self.video_txt, f"Video {key} not found")
        self.status_lbl.configure(text="Check Video button was clicked!")

    try:
        def __init__(self, parent, controller):
            Frame.__init__(self, parent, bg="#d2e0e0")
            self.controller = controller

            heading_label = Label(self, text="Video Player", font=("arial", 40, "bold"), fg="black", bg="#d2e0e0")
            heading_label.pack(pady=25)

            main_menu_lbl = Label(self, text="Update Videos Ratings", font=("arial", 15, "bold"), fg="black",
                                  bg="#d2e0e0")
            main_menu_lbl.pack()

            button_frame = Frame(self, bg="#c3d5d5")
            button_frame.pack(fill="both", expand=True)

            enter_lbl = Label(button_frame, text="Enter Video Number :", font=("arial", 20), fg="black", bg="#c3d5d5")
            enter_lbl.grid(row=0, column=0, sticky="W", padx=10, pady=10)

            enter_lbl = Label(button_frame, text="New Rating :", font=("arial", 20), fg="black", bg="#c3d5d5")
            enter_lbl.grid(row=1, column=0, sticky="W", padx=10, pady=10)

            self.input_txt = Entry(button_frame, width=5, fg="black", bg="#c3d5d5")
            self.input_txt.grid(row=0, column=1, padx=20, pady=10)

            self.input_rating_txt = Entry(button_frame, width=5, fg="black", bg="#c3d5d5")
            self.input_rating_txt.grid(row=1, column=1, padx=20, pady=10)

            check_video_btn = Button(button_frame, text="Check Video", command=self.check_video_clicked,
                                     relief="raised", borderwidth=3, width=30, height=3, fg="black")
            check_video_btn.grid(row=0, column=2, padx=60, pady=10)

            update_video_btn = Button(button_frame, text="Update Video", command=self.update_video_clicked,
                                      relief="raised", borderwidth=3, width=30, height=3, fg="black")
            update_video_btn.grid(row=1, column=2, padx=60, pady=10)

            self.video_txt = Text(button_frame, width=35, height=10, wrap="none", fg="black", bg="#c3d5d5")
            self.video_txt.grid(row=0, column=3, sticky="NW", padx=10, pady=10)

            self.status_lbl = Label(button_frame, text="", fg="black", bg="#c3d5d5")
            self.status_lbl.grid(row=4, column=0, columnspan=4, sticky="W", padx=10, pady=10)

            def back():
                controller.show_frame("VideoMenu")

            exit_button = Button(button_frame, font=("arial", 15), text="Back", command=back, relief="raised",
                                 borderwidth=3, width=15, height=1, fg="black", bg="#c3d5d5")
            exit_button.grid(row=4, column=2, padx=30, pady=10)
    except AttributeError:
        messagebox.showinfo(f"Sorry, could not open GUI")


class CreateVideoList(Frame):
    def add_to_playlist_clicked(self):
        try:
            key = self.input_txt.get()
            name = lib.get_name(key)
            if name is not None:
                director = lib.get_director(key)
                rating = lib.get_rating(key)
                play_count = lib.get_play_count(key)
                video_details = f"{name}\n{director}\nrating: {rating}\nplays: {play_count}"
                set_text(self.video_txt, video_details)
                self.playlist.append(key)
                self.display_playlist()
            else:
                set_text(self.video_txt, f"Video {key} not found")
            self.status_lbl.configure(text="Add video to playlist button was clicked!")
        except AttributeError:
            messagebox.showinfo(f"Sorry,the video could not be added to the playlist")

    def list_videos_clicked(self):
        try:
            video_list = lib.list_all()
            set_text(self.list_txt, video_list)
            self.status_lbl.configure(text="List Videos button was clicked!")
        except AttributeError:
            messagebox.showinfo(f"Error: Sorry, could not list the videos")

    def display_playlist(self):
        try:
            output = "Playlist:\n"
            for key in self.playlist:
                name = lib.get_name(key)
                play_count = lib.get_play_count(key)
                output += f"{key} {name} [{play_count} plays]\n"
                set_text(self.list_txt, output)
            self.status_lbl.configure(text="Show play list button was clicked!")
        except AttributeError:
            messagebox.showinfo(f"Error: Could not display the playlist")

    def play_playlist(self):
        try:
            for key in self.playlist:
                lib.increment_play_count(key)
            self.display_playlist()
            self.status_lbl.configure(text="Play the playlist button was clicked!")
        except AttributeError:
            messagebox.showinfo(f"Error: Could not play the playlist")

    def reset_playlist(self):
        try:
            self.playlist.clear()
            set_text(self.list_txt, "Playlist:\n")
            self.status_lbl.configure(text="Reset the playlist button was clicked!")
        except AttributeError:
            messagebox.showinfo(f"Error: Playlist is empty")

    try:
        def __init__(self, parent, controller):
            Frame.__init__(self, parent, bg="#d2e0e0")
            self.controller = controller
            self.playlist = []

            heading_label = Label(self, text="Video Player", font=("arial", 40, "bold"), fg="black", bg="#d2e0e0")
            heading_label.pack(pady=25)

            main_menu_lbl = Label(self, text="Create a Playlist", font=("arial", 15, "bold"), fg="black", bg="#d2e0e0")
            main_menu_lbl.pack()

            button_frame = Frame(self, bg="#c3d5d5")
            button_frame.pack(fill="both", expand=True)

            list_videos_btn = Button(button_frame, text="List All Videos", command=self.list_videos_clicked,
                                     relief="raised", borderwidth=3, width=30, height=3, fg="black")
            list_videos_btn.grid(row=0, column=0, padx=10, pady=10)

            enter_lbl = Label(button_frame, text="Enter Video Number :", font=("arial", 15), fg="black", bg="#c3d5d5")
            enter_lbl.grid(row=0, column=1, padx=10, pady=10)

            self.input_txt = Entry(button_frame, width=5, fg="black", background="#c3d5d5")
            self.input_txt.grid(row=0, column=2)

            add_to_playlist_btn = Button(button_frame, text="Add to Playlist", command=self.add_to_playlist_clicked,
                                         relief="raised", borderwidth=3, width=30, height=3, fg="black")
            add_to_playlist_btn.grid(row=0, column=3, padx=10, pady=10)

            self.list_txt = tkst.ScrolledText(button_frame, width=60, height=22, wrap="none", fg="black",
                                              bg="#c3d5d5")
            self.list_txt.grid(row=1, column=0, columnspan=2, sticky="W", padx=10, pady=10)

            self.video_txt = Text(button_frame, width=40, height=12, wrap="none", fg="black", bg="#c3d5d5")
            self.video_txt.grid(row=1, column=3, sticky="NW", padx=10, pady=10)

            display_playlist_btn = Button(button_frame, text="Display the Playlist", command=self.display_playlist,
                                          relief="raised", borderwidth=3, width=30, height=3, fg="black")
            display_playlist_btn.grid(row=0, column=4, padx=10, pady=10)

            play_list_btn = Button(button_frame, text="Play the Playlist", command=self.play_playlist, relief="raised",
                                   borderwidth=3, width=30, height=3, fg="black")
            play_list_btn.grid(row=2, column=3, padx=10, pady=10)

            reset_list_btn = Button(button_frame, text="Reset the Playlist", command=self.reset_playlist,
                                    relief="raised", borderwidth=3, width=30, height=3, fg="black")
            reset_list_btn.grid(row=2, column=4, padx=10, pady=10)

            self.status_lbl = Label(button_frame, text="", fg="black", background="#c3d5d5")
            self.status_lbl.grid(row=2, column=0, columnspan=2, padx=10, pady=10)

            def back():
                controller.show_frame("VideoMenu")

            exit_button = Button(button_frame, font=("arial", 15), text="Back", command=back, relief="raised",
                                 borderwidth=3, width=13, height=1, fg="black")
            exit_button.grid(row=3, column=0, padx=30, pady=10)
    except AttributeError:
        messagebox.showinfo(f"Sorry, could not open GUI")


if __name__ == "__main__":
    app = SampleApp()
    app.mainloop()
