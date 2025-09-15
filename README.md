# File-Operator
import os
import datetime
from typing import List, Optional

class JournalManager:
    """
    A class to manage personal journal entries with file operations.
    Handles adding, viewing, searching, and deleting journal entries.
    """
    
    def _init_(self, filename: str = "journal.txt"):
        """
        Initialize the JournalManager with a specified filename.
        
        Args:
            filename (str): Name of the journal file. Defaults to "journal.txt"
        """
        self.filename = filename
        self.ensure_file_exists()
    
    def ensure_file_exists(self) -> None:
        """
        Ensure the journal file exists. Create it if it doesn't exist.
        """
        try:
            if not os.path.exists(self.filename):
                with open(self.filename, 'w', encoding='utf-8') as file:
                    file.write("")  # Create empty file
                print(f"Created new journal file: {self.filename}")
        except OSError as e:
            print(f"Error creating journal file: {e}")
            raise
    
    def add_entry(self, entry_text: str) -> bool:
        """
        Add a new journal entry with timestamp.
        
        Args:
            entry_text (str): The journal entry content
            
        Returns:
            bool: True if entry was added successfully, False otherwise
        """
        try:
            timestamp = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            formatted_entry = f"[{timestamp}] {entry_text}\n"
            
            with open(self.filename, 'a', encoding='utf-8') as file:
                file.write(formatted_entry)
            
            print("Journal entry added successfully!")
            return True
            
        except OSError as e:
            print(f"Error adding entry to journal: {e}")
            return False
        except Exception as e:
            print(f"Unexpected error while adding entry: {e}")
            return False
    
    def view_all_entries(self) -> None:
        """
        Display all journal entries from the file.
        Handles the case where the file does not exist or is empty.
        """
        try:
            with open(self.filename, 'r', encoding='utf-8') as file:
                content = file.read().strip()
            
            if not content:
                print("No journal entries found. The journal is empty.")
                return
            
            print("\n" + "="*50)
            print("ALL JOURNAL ENTRIES")
            print("="*50)
            print(content)
            print("="*50)
            
        except FileNotFoundError:
            print("Journal file not found. No entries to display.")
        except OSError as e:
            print(f"Error reading journal file: {e}")
        except Exception as e:
            print(f"Unexpected error while reading entries: {e}")
    
    def search_entries(self, search_term: str) -> None:
        """
        Search for entries containing a specific keyword or date.
        
        Args:
            search_term (str): The term to search for in journal entries
        """
        try:
            with open(self.filename, 'r', encoding='utf-8') as file:
                lines = file.readlines()
            
            if not lines:
                print("No journal entries found. The journal is empty.")
                return
            
            matching_entries = []
            for line in lines:
                if search_term.lower() in line.lower():
                    matching_entries.append(line.strip())
            
            if matching_entries:
                print(f"\n" + "="*50)
                print(f"SEARCH RESULTS for '{search_term}'")
                print("="*50)
                for entry in matching_entries:
                    print(entry)
                print("="*50)
                print(f"Found {len(matching_entries)} matching entry(ies).")
            else:
                print(f"No entries found containing '{search_term}'.")
                
        except FileNotFoundError:
            print("Journal file not found. No entries to search.")
        except OSError as e:
            print(f"Error reading journal file: {e}")
        except Exception as e:
            print(f"Unexpected error while searching: {e}")
    
    def delete_all_entries(self) -> bool:
        """
        Clear the journal by deleting the file after user confirmation.
        
        Returns:
            bool: True if entries were deleted successfully, False otherwise
        """
        try:
            # Check if file exists and has content
            if not os.path.exists(self.filename):
                print("No journal file exists. Nothing to delete.")
                return False
            
            with open(self.filename, 'r', encoding='utf-8') as file:
                content = file.read().strip()
            
            if not content:
                print("Journal is already empty. Nothing to delete.")
                return False
            
            # Prompt for confirmation
            print("\n⚠  WARNING: This will permanently delete ALL journal entries!")
            confirmation = input("Are you sure you want to continue? (yes/no): ").strip().lower()
            
            if confirmation in ['yes', 'y']:
                os.remove(self.filename)
                self.ensure_file_exists()  # Create new empty file
                print("All journal entries have been deleted successfully.")
                return True
            else:
                print("Deletion cancelled.")
                return False
                
        except OSError as e:
            print(f"Error deleting journal entries: {e}")
            return False
        except Exception as e:
            print(f"Unexpected error while deleting entries: {e}")
            return False
    
    def get_entry_count(self) -> int:
        """
        Get the number of entries in the journal.
        
        Returns:
            int: Number of entries in the journal
        """
        try:
            with open(self.filename, 'r', encoding='utf-8') as file:
                lines = file.readlines()
            return len([line for line in lines if line.strip()])
        except FileNotFoundError:
            return 0
        except Exception:
            return 0


def display_menu() -> None:
    """Display the main menu options to the user."""
    print("\n" + "="*40)
    print("Personal Journal Manager")
    print("="*40)
    print("1. Add a New Entry")
    print("2. View All Entries")
    print("3. Search for an Entry")
    print("4. Delete All Entries")
    print("5. Exit")
    print("="*40)


def get_user_choice() -> str:
    """
    Get and validate user menu choice.
    
    Returns:
        str: Valid menu choice (1-5)
    """
    while True:
        choice = input("Please select an option (1-5): ").strip()
        if choice in ['1', '2', '3', '4', '5']:
            return choice
        print("❌ Invalid choice. Please enter a number between 1 and 5.")


def main():
    """
    Main function to run the Personal Journal Manager application.
    Implements a menu-driven interface for user interaction.
    """
    print("Welcome to Personal Journal Manager!")
    
    try:
        # Initialize the journal manager
        journal = JournalManager()
        
        # Display initial status
        entry_count = journal.get_entry_count()
        if entry_count > 0:
            print(f"Found {entry_count} existing journal entry(ies).")
        
        while True:
            display_menu()
            choice = get_user_choice()
            
            if choice == '1':
                # Add a New Entry
                print("\n📝 Add New Journal Entry")
                print("-" * 30)
                entry = input("Enter your journal entry: ").strip()
                
                if entry:
                    journal.add_entry(entry)
                else:
                    print("❌ Entry cannot be empty. Please try again.")
            
            elif choice == '2':
                # View All Entries
                print("\n📖 Viewing All Journal Entries")
                print("-" * 30)
                journal.view_all_entries()
            
            elif choice == '3':
                # Search for an Entry
                print("\n🔍 Search Journal Entries")
                print("-" * 30)
                search_term = input("Enter keyword or date to search for: ").strip()
                
                if search_term:
                    journal.search_entries(search_term)
                else:
                    print("❌ Search term cannot be empty. Please try again.")
            
            elif choice == '4':
                # Delete All Entries
                print("\n🗑  Delete All Journal Entries")
                print("-" * 30)
                journal.delete_all_entries()
            
            elif choice == '5':
                # Exit
                print("\n👋 Thank you for using Personal Journal Manager!")
                print("Your entries have been saved to journal.txt")
                break
    
    except KeyboardInterrupt:
        print("\n\n👋 Program interrupted. Your entries have been saved.")
    except Exception as e:
        print(f"\n❌ An unexpected error occurred: {e}")
        print("Please restart the program.")


if _name_ == "_main_":
    main()
