#!/usr/bin/python
from tempfile import mkstemp
from shutil import copyfile
from filewatch import ObserverBase, file_updated_subject, Watcher
from os import fdopen, remove, path

# height of viewport in dp
dp_height = 640.0
# width of viewport in dp
dp_width = 360.0

# modifiers for simple conversion of dp to vh/vw
dph_mod = round((1 / dp_height) * 100, 5)
dpw_mod = round((1 / dp_width) * 100, 5)

# export path for processed CSS to be saved
# Note: should be minimum 1 level above the CWD
export_path = "../dist"

# Main function for calling converters based on CSS styles
def process(line):
    if 'height:' in line:
        return convert_dp2vh(line)

    elif 'top:' in line or 'bottom:' in line:
        return convert_dp2vh(line)

    elif 'width:' in line:
        return convert_dp2vw(line)

    elif 'left:' in line or 'right:' in line:
        return convert_dp2vw(line)
    else:
        return line

# Function for converting dp to vh and string functions
def convert_dp2vh(line):
    for t in line.split():
        if 'dp' in t:
            if 'dp;' in t:
                valh = t.replace("dp;", "")
            else:
                valh = t.replace("dp", "")

            try:

                if '.' in valh or '-' in valh:
                    valh = float(valh)
                    nvalh = round(valh  * dph_mod, 2)
                    n_line = line.replace(str(valh), str(nvalh))
                    n_line = n_line.replace("dp", "vh")
                    return str(n_line)

                else:
                    valh = int(valh)
                    nvalh = round(valh  * dph_mod, 2)
                    n_line = line.replace(str(valh), str(nvalh))
                    n_line = n_line.replace("dp", "vh")
                    return str(n_line)

            except ValueError:
                return t

# Function for converting dp to vw and string functions
def convert_dp2vw(line):
    for t in line.split():
        if 'dp' in t:
            if 'dp;' in t:
                valw = t.replace("dp;", "")
            else:
                valw = t.replace("dp", "")

            try:

                if '.' in valw:
                    valw = float(valw)
                    nvalw = round(valw  * dpw_mod, 2)
                    n_line = line.replace(str(valw), str(nvalw))
                    n_line = n_line.replace("dp", "vw")
                    return str(n_line)

                else:
                    valw = int(valw)
                    nvalw = round(valw  * dpw_mod, 2)
                    n_line = line.replace(str(valw), str(nvalw))
                    n_line = n_line.replace("dp", "vw")
                    return str(n_line)

            except ValueError:
                return t

# Function for replacing each line of a file, with processed lines
def replacer(file_path):
    #Create temp file
    fh, abs_path = mkstemp()
    with fdopen(fh,'w') as new_file:
        with open(file_path) as old_file:
            for line in old_file:
                new_line = process(line)
                new_file.write(line.replace(str(line), str(new_line)))
    copyfile(abs_path, "%s/%s" % (export_path, path.basename(file_path)))
    remove(abs_path)

# Main Observer class from the module: filewatch
class YourObserver(ObserverBase):

    # Function which checks changed files and processes them
    def notify(self, *args, **kwargs):
        file_list = kwargs['file_list']
        print ""
        print 'This files has been updated: %s' % path.basename(str(file_list[0]))
        print 'Editing file: %s ...' % path.basename(str(file_list[0]))
        replacer(str(file_list[0]))
        print "Successfully edited file"
        print "Successfully copied file to: %s/%s" % (export_path, path.basename(str(file_list[0])))
        print ""
        print "Continuing Watcher..."

file_updated_subject.register_observer(YourObserver())
watcher = Watcher()
print "Running Watcher..."
watcher.run()
