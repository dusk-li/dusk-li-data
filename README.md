# dusk-li-data

Each yaml file contains data in the following schema:

**company**: *Site or Company name*

**category**: *Site category as assessed by [Symantec Sitereview](https://sitereview.bluecoat.com/)*

**url**: *https://www.example.com*

**description**: *Brief description of the website*

**dark-mode**: *None | Manual | Auto (0 Points | 1 Point | 2 Points - substract half a point if the entire site doesn't support dark mode)*

**default-theme-style**: *Bright | Mixed | Dark (0 Points | 1 Point | 2 Points)*

**supports-custom-themes**: *No | LoggedIn | Yes (0 Points | 1 Point | 2 Points)*

**accessibility-rating**: *Total of the above scores*

**last-updated**: *YYYY-MM-DD*