import streamlit as st

# Initialize session state variables
if "global_depth" not in st.session_state:
    st.session_state.global_depth = 1  # Start with global depth 1
if "bucket_size" not in st.session_state:
    st.session_state.bucket_size = 2
if "directory" not in st.session_state:
    # By default, buckets 0 and 1 (with empty lists for each bucket)
    st.session_state.directory = {'0': [], '1': []}
if "stored_values" not in st.session_state or not isinstance(st.session_state.stored_values, list):
    st.session_state.stored_values = []  # Initialize with an empty list if not present

# Hashing function to get the index based on the current global depth
def hash_value(value, depth):
    return bin(value % (2 ** depth))[2:].zfill(depth)

# Function to insert a value into the hash table
def insert_value(value):
    st.session_state.stored_values.append(value)  # Correctly add value to the list of stored values
    rehash()  # Rehash to accommodate the new value
    st.experimental_rerun()  # Refresh the display

# Function to delete a value from the hash table
def delete_value(value):
    if value in st.session_state.stored_values:
        st.session_state.stored_values.remove(value)  # Remove value if it exists
        rehash()  # Rehash to accommodate the removed value
        st.experimental_rerun()  # Refresh the display

# Rehash function to handle bucket splits and re-inserts
def rehash():
    # Reset the directory and clear all existing buckets
    global_depth = st.session_state.global_depth
    directory = st.session_state.directory  # Reference the directory

    # Clear all existing buckets
    for bucket in directory:
        directory[bucket].clear()

    # Insert all values into the current directory
    for value in st.session_state.stored_values:
        index = hash_value(value, global_depth)

        # If the bucket has space, add the value
        if len(directory[index]) < st.session_state.bucket_size:
            directory[index].append(value)
        else:
            # Perform a bucket split
            st.session_state.global_depth += 1  # Increase global depth
            global_depth = st.session_state.global_depth

            # Re-create the directory with more bits (double the number of buckets)
            new_directory = {}
            for i in range(2 ** global_depth):
                new_directory[bin(i)[2:].zfill(global_depth)] = []  # Initialize new empty buckets

            # Re-insert all values based on new hash values
            for val in st.session_state.stored_values:
                new_index = hash_value(val, global_depth)
                new_directory[new_index].append(val)

            # Update the session state with the new directory
            st.session_state.directory = new_directory
            break

# Main page showing global depth first, then the current directory
st.title("Extensible Hashing Simulation")

# Displaying Global Depth
st.write(f"### Global Depth: {st.session_state.global_depth}")

# Displaying Current Directory after Global Depth
st.write("### Current Directory (Buckets)")
directory_html = ""
for bucket, values in st.session_state.directory.items():
    directory_html += f"<div style='font-size:20px;'>Bucket {bucket}: {values}</div>"
st.markdown(directory_html, unsafe_allow_html=True)

# Sidebar for Insert and Delete operations
st.sidebar.header("Insert/Delete Values")

# Insert Value
insert_value_input = st.sidebar.text_input("Insert a value:")
if st.sidebar.button("Insert"):
    if insert_value_input.isdigit():
        insert_value(int(insert_value_input))  # Convert input to integer
        st.sidebar.success(f"Value {insert_value_input} inserted.")
    else:
        st.sidebar.error("Please enter a valid integer.")

# Delete Value
delete_value_input = st.sidebar.text_input("Delete a value:")
if st.sidebar.button("Delete"):
    if delete_value_input.isdigit():
        delete_value(int(delete_value_input))  # Convert input to integer
        st.sidebar.success(f"Value {delete_value_input} deleted.")
    else:
        st.sidebar.error("Please enter a valid integer.")
